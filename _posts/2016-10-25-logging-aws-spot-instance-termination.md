---
layout: post
title:  "Logging AWS spot instance termination"
number: 23
date:   2016-10-25 2:00
categories: monitoring
---
Logging the fact that AWS is going to terminate your EC2 spot instance is very important. If along with that information, you can save the instance metadata, it allows for some very interesting analytics. For example, you could find out the AZ in which AWS is revoking your instances the most.

I'm currently using the following script:

```php
<?php
/**
 * scale_down_log.php
 *
 * Script will run automatically in the background and monitor AWS instance metadata for spot termination of the current machine.
 * If it is going to be terminated, it will send instance data to an API.
 *
 * @author     @ayush_sharma
 */

# Set error reporting
error_reporting ( E_ALL );
ini_set ( 'display_errors', 'On' );

# Begin!
do {

    $is_spot = shell_exec ( 'curl -s http://169.254.169.254/latest/meta-data/spot/termination-time' );

    if ( strpos ( $is_spot, 'T' ) !== FALSE && strpos ( $is_spot, 'Z' ) !== FALSE ) {

        insert_log ( );
        exit();
    }

    sleep ( 5 );
}
while ( 1 == 1 );

/**
 * Function will send instance metadata to an API.
 */
function insert_log ()
{
    $up_time = shell_exec ( 'cat /proc/uptime | awk \'{print $1}\'' );
    $up_time = trim ( preg_replace ( '/\s\s+/', ' ', $up_time ) );

    $spot_termination_time = convertTimestamp ( shell_exec ( 'curl -s http://169.254.169.254/latest/meta-data/spot/termination-time' ) );

    $fields = [
        'application'           => 'my_app',
        'event'                 => 'spot_termination',
        'event_comments'        => 'some comments',
        'instance_id'           => shell_exec ( 'curl -s http://169.254.169.254/latest/meta-data/instance-id' ),
        'instance_type'         => shell_exec ( 'curl -s http://169.254.169.254/latest/meta-data/instance-type' ),
        'az'                    => shell_exec ( 'curl -s http://169.254.169.254/latest/meta-data/placement/availability-zone' ),
        'up_time'               => $up_time,
        'spot_termination_time' => $spot_termination_time,
        'command_time'          => time ()
    ];

    $url = 'http://myapi.example.com/spot_termination_data?data=' . urlencode ( json_encode ( $fields ) );

    shell_exec ( 'curl -s ' . $url );

    echo 'Data inserted.';
}

/**
 * Function to convert AWS timestamp to UNIX timestamp.
 *
 * @param $a
 * @return false|int
 */
function convertTimestamp ( $a )
{
    $tmp = explode ( 'T', $a );

    $date = explode ( '-', $tmp[ 0 ] );
    $time = explode ( ':', substr ( $tmp[ 1 ], 0, 8 ) );

    $timestamp = mktime ( $time[ 0 ], $time[ 1 ], $time[ 2 ], $date[ 1 ], $date[ 2 ], $date[ 0 ] );

    return $timestamp;
}
```

You can easily run this script in the background using `nohup php scale_down_log.php &`.