NoxlogicRateLimitBundle
========================

[![Build Status](https://travis-ci.org/jaytaph/RateLimitBundle.svg?branch=master)](https://travis-ci.org/jaytaph/RateLimitBundle)
[![Code Coverage](https://scrutinizer-ci.com/g/jaytaph/RateLimitBundle/badges/coverage.png?b=master)](https://scrutinizer-ci.com/g/jaytaph/RateLimitBundle/?branch=master)
[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/jaytaph/RateLimitBundle/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/jaytaph/RateLimitBundle/?branch=master)
[![Latest Stable Version](https://poser.pugx.org/noxlogic/ratelimit-bundle/v/stable.svg)](https://packagist.org/packages/noxlogic/ratelimit-bundle) [![Total Downloads](https://poser.pugx.org/noxlogic/ratelimit-bundle/downloads.svg)](https://packagist.org/packages/noxlogic/ratelimit-bundle) [![Latest Unstable Version](https://poser.pugx.org/noxlogic/ratelimit-bundle/v/unstable.svg)](https://packagist.org/packages/noxlogic/ratelimit-bundle) [![License](https://poser.pugx.org/noxlogic/ratelimit-bundle/license.svg)](https://packagist.org/packages/noxlogic/ratelimit-bundle)

This bundle provides enables the @ratelimit annotation which allows you to limit the number of connections to actions.
This is mostly useful in APIs. All information is currently only available to be stored in Redis, but adding other storage systems should be not difficult.

Right now, the bundle will work in corporation with the FOSOAuthServerBundle. It contains a listener that adds the oauth token to the cache-key, but it's easy to implement your own listener that will use your setup (like the user that has logged in for instance, when you don't use oauth). Look for the `ratelimit.generate.key` event.

This bundle is partially inspired by a github gist from Ruud Kamphuis: https://gist.github.com/ruudk/3350405

## Features

 * Simple usage through annotations
 * Customize rates per controller, action and even per HTTP method
 * Multiple storage backends: redis, memcached etc


## Installation

Installation takes just few easy steps:

### Step 1: Add the bundle to your composer.json

If you're not yet familiar with Composer see http://getcomposer.org.
Add the NoxlogicRateLimitBundle in your composer.json:

```js
{
    "require": {
        "noxlogic/ratelimit-bundle": "@dev-master"
    }
}
```

Now tell composer to download the bundle by running the command:

``` bash
php composer.phar update noxlogic/ratelimit-bundle
```

### Step 2: Enable the bundle

Enable the bundle in the kernel:

``` php
<?php
// app/AppKernel.php

public function registerBundles()
{
    $bundles = array(
        // ...
        new Noxlogic\RateLimitBundle\NoxlogicRateLimitBundle(),
    );
}
```




## Details

### Simple rate limiting

To enable rate limiting, you only need to add the annotation to the docblock of the specified action

    /**
     * @route(...)
     *
     * @ratelimit(limit=1000, period=3600)
     */
    public function someApiAction() { ... }



### Limit per method

It's possible to rate-limit specific HTTP methods as well. This can be either a string or an array of methods. When no
method argument is given, all other methods not defined are rated. This allows to add a default rate limit if needed.

    /**
     * @route(...)
     *
     * @ratelimit(methods={ PUT,POST }, limit=1000, period=3600)
     * @ratelimit(methods="GET", limit=1000, period=3600)
     * @ratelimit(limit=5000, period=3600)
     */
    public function someApiAction() { ... }



### Limit per controller

It's also possible to add rate-limits to a controller class instead of a single action. This will act as a default rate
limit for all actions, except the ones that actually defines a custom rate-limit.

    /**
     * @ratelimit(method="POST", limit=100, period=10);        // 100 POST requests per 10 seconds
     */
   class DefaultController extends Controller
   {

        /**
         * @ratelimit(method="POST", limit=200, period=10);        // 200 POST requests to indexAction allowed.
         */
        function indexAction() {
        }

   }



## TODO

  * Better documentation
  * Add default x-rate limit to config (like: rate-limiting an /api directory for instance)
  * Split backends in a better way (right now, it depends on redis too much)
  * Add memcache storage
  * Unittests
