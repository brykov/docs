# Functional Mode <Badge text="new" />

<!-- ::: info Video Docs
Learn about leaf 3's new functional mode.

<VideoLesson href="#" title="Functional mode">Watch the functional mode guide on youtube</VideoLesson>
::: -->

"Functional Mode", as we call it is just a fancy name given to a bunch of predefined functions in your leaf app which allow you to create your entire app or API without relying on classes with lengthy initializers and namespaces. With functional mode, everything is handled for you under the hood and is given to you in one global function.

Functional mode is 100% optional as seen in the examples in the introduction section. It also requires zero setup or configuration since it's available right after installing Leaf.

```php
<?php

require __DIR__ . "/vendor/autoload.php";

app()->get("/", function () {
  response(["name" => "Leaf"]);
});

app()->run();
```

## How to configure leaf

Since leaf provides extra options which can be passed into leaf on init, how do you configure the leaf object if you have no direct access to it?

- Use the config method

There is a configuration method on leaf which allows you to set extra configurations if needed. You might never have to use this, but depending on the complexities to tackle, you might want Leaf to behave a certain way. You can call the `config` method on `app()` to set these application config.

```php
<?php

require __DIR__ . "/vendor/autoload.php";

app()->config(["app.down" => true]);

app()->get("/", function () {
  response(["name" => "Leaf"]);
});

app()->run();
```

- Use the config class

Besides this, you can use the `Leaf\Config` class to pass in configuration options

```php
<?php

require __DIR__ . "/vendor/autoload.php";

Leaf\Config::set("app.down", true);

app()->get("/", function () {
  response(["name" => "Leaf"]);
});

app()->run();
```

The main difference here is that the second method sets the config before the Leaf app is initialized. This means that during initialization, Leaf will use the config that has been set, however, for the first method, the config is loaded only after Leaf is initialized and thus, not used in the initialization process.

## app

This function returns the current instance of the Leaf application. If none exists, it creates and returns it.

```php{4}
$app = new Leaf\App;

$app->get("/", function () {
  app()->response()->json(["name" => "Leaf"]);
});
```

`app()` on line 4 will return the leaf instance `$app` defined on line 1. If however, no leaf instance is found, `app()` will create a new instance and return it.

```php{5}
<?php

require __DIR__ . "/vendor/autoload.php";

app()->get("/", function () {
  response(["name" => "Leaf"]);
});

app()->run();
```

As seen on line 5, no app instance already exists and so one is created and returned. This gives you powerful tooling and lets you get rid of imports, namespaces...

::: warning NOTE
In other frameworks like laravel which also ship an `app` method, calling `app` will return the laravel instance, not leaf's. In such situations, you can use the [app instance config](/docs/config/nsm#config-app-instance)
:::

## _env

This global function allows you to get environment variables set in your `.env` file. `_env` takes in 2 parameters:

- The name of the env variable to get (required)
- The default value of the env variable to get if it doesn't exist (optional)

```php
// get value
$mode = _env("APP_MODE");

// get value with default
$mode = _env("APP_MODE", "production");
```

## Extending

These are the globals provided by default with Leaf, however, some Leaf modules come in with their own globals, for instance, `leafs/session` has the `session` and `flash` globals. All globals are named carefully to avoid conflicts with other popular PHP packages.

::: warning NOTE
Leaf and it's modules **only** set a global if a function with that name doesn't exist. This is to avoid unintentionally overwriting important functions in your code.
:::

If you run into a challenge like this, you can rename your functions if you defined them yourself.

::: tip Extending
Modules which extend functional mode will have a section on their documentation with a functional mode tag. You can always look out for that
:::

## request

`request` is a 2 way global function which allows you to either get data passed into your application or return the leaf request object.

::: warning NOTE
The request global is provided from the [leaf http module](/modules/http). No need to install it since it comes with leaf 3 out of the box.
:::

```php
// return the username variable passed into the request
$username = request("username");
```

If no value is passed into request, the leaf request object is returned.

```php
$username = request()->get("username");
```

## response

This global allows you to output json encoded data or return the leaf response object.

::: warning NOTE
The response global is provided from the [leaf http module](/modules/http). No need to install it since it comes with leaf 3 out of the box.
:::

```php
// output json data
response(["name" => "Mychi"]);
```

Just as with request above, if nothing is passed into `response`, it returns the leaf response object.

```php
response()->markup("<b>Mychi</b>");
```

## cookie

This global allows you to set/get a cookie or return the leaf cookie object.

::: warning NOTE
The cookie global is provided from the [leaf cookie module](/modules/cookies).
:::

```php
// set multiple cookies
cookie(["name" => "Mychi", "code" => "PHP"]);

// set single cookie
cookie("name", "Mychi");

// get cookie
cookie("name");
```

Just as with request above, if nothing is passed into `cookie`, it returns the leaf cookie object.

```php
cookie()->set("name", "Mychi");
```

## Leaf CSRF

Leaf CSRF provides 2 globals which make working with the package a whole lot easier.

::: warning NOTE
To get started you will need the [leaf csrf module](/modules/anchor/csrf/).
:::

### _token

This method returns a generated CSRF token.

```php
$token = _token();
```

### _csrfField

This method generates a CSRF field in a form for you.

```php
<form>
  <?php _csrfField(); ?>
  ...
</form>
```
