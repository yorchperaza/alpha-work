# Stack/Cors

Library and middleware enabling cross-origin resource sharing for your
http-{foundation,kernel} using application. It attempts to implement the
[W3C Recommendation] for cross-origin resource sharing.

[W3C Recommendation]: http://www.w3.org/TR/cors/

Master [![Build Status](https://secure.travis-ci.org/asm89/stack-cors.png?branch=master)](http://travis-ci.org/asm89/stack-cors)

## Installation

Require `asm89/stack-cors` using composer.

## Usage

This package can be used as a library or as [stack middleware].

[stack middleware]: http://stackphp.com/

### Example: using the library

```php
<?php

use Asm89\Stack\CorsService;

$cors = new CorsService(array(
    'allowedHeaders'      => array('x-allowed-header', 'x-other-allowed-header'),
    'allowedMethods'      => array('DELETE', 'GET', 'POST', 'PUT'),
    'allowedOrigins'      => array('localhost'),
    'exposedHeaders'      => false,
    'maxAge'              => false,
    'supportsCredentials' => false,
));

$cors->addActualRequestHeaders(Response $response, $origin);
$cors->handlePreflightRequest(Request $request);
$cors->isActualRequestAllowed(Request $request);
$cors->isCorsRequest(Request $request);
$cors->isPreflightRequest(Request $request);
```

## Example: using the stack middleware

```php
<?php

use Asm89\Stack\Cors;

$app = new Cors($app, array(
    // you can use array('*') to allow any headers
    'allowedHeaders'      => array('x-allowed-header', 'x-other-allowed-header'),
    // you can use array('*') to allow any methods
    'allowedMethods'      => array('DELETE', 'GET', 'POST', 'PUT'),
    // you can use array('*') to allow requests from any origin
    'allowedOrigins'      => array('localhost'),
    'exposedHeaders'      => false,
    'maxAge'              => false,
    'supportsCredentials' => false,
));
```
