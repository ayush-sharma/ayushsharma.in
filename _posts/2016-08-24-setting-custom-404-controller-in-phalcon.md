---
layout: post
title:  "Setting Custom 404 Controller in Phalcon"
number: 17
date:   2016-08-24 2:00
categories: development
---
This works for Phalcon 2.0.13.

You can set up a custom 404 controller and method in Phalcon using the [Dispatcher](https://docs.phalconphp.com/en/latest/api/Phalcon_Mvc_Dispatcher.html). Just replace the CONTROLLER_NAME and METHOD_NAME in the code below with your choice of controller and method.

```php
$this->di->set ( 'dispatcher', function () {

    $eventsManager = new \Phalcon\Events\Manager();

    $eventsManager->attach ( "dispatch:beforeException", function ( $event, $dispatcher, $exception ) {

        // Handle 404
        if ( $exception instanceof \Phalcon\Mvc\Dispatcher\Exception ) {
            $dispatcher->forward ( array (
                                       'controller' => 'CONTROLLER_NAME',
                                       'action'     => 'METHOD_NAME'
                                   ) );

            return FALSE;
        }

        // Handle others
        $dispatcher->forward ( array (
                                   'controller' => 'CONTROLLER_NAME',
                                   'action'     => 'METHOD_NAME'
                               ) );

        return FALSE;
    } );

    $dispatcher = new \Phalcon\Mvc\Dispatcher();

    //Bind the EventsManager to the dispatcher
    $dispatcher->setEventsManager ( $eventsManager );

    return $dispatcher;

}, TRUE );
```