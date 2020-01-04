---
layout: post
title:  "Reading JWT token in Phalcon"
number: 18
date:   2016-08-24 3:00
categories: development
---
This works in Phalcon 2.0.13.

While I was working with JWT and Phalcon, I found that reading the `HTTP Authorization` header was not a cake walk. This is what my attempt looked like. The code isn't glorious, but it got the job done. Hope this helps someone else.

```php
/*
 * Get Token
 *
 * Reads the Authorization header BY ANY MEANS NECESSARY and returns the token contained within.
 *
 * @access  private
 * @param   object  $request    Phalcon request object.
 * @return  string
 */
private function _getToken ( $request )
{
    $token       = NULL;
    $auth_header = $request->getServer ( 'HTTP_AUTHORIZATION' );

    if ( empty( $auth_header ) ) {

        $auth_header = $request->getHeader ( 'Authorization' );

        if ( empty( $auth_header ) ) {

            $auth_header = $request->getHeader ( 'AUTHORIZATION' );

            if ( empty( $auth_header ) ) {

                $auth_header = $request->getHeader ( 'REDIRECT_HTTP_AUTHORIZATION' );
            }
        }
    }

    if ( ! empty( $auth_header ) ) {

        $auth_header = explode ( ' ', $auth_header );
        if ( count ( $auth_header ) == 2 ) {

            $token = $auth_header[ 1 ];
        }
    }

    return $token;
}
```