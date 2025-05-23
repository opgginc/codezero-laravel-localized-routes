# Upgrade Guide

## Upgrading To 5.0 From 4.x

### ➡ Package Name Changed

The package name has been changed from `codezero/laravel-localized-routes` to `opgginc/codezero-laravel-localized-routes`.

🔸 **Actions Required**

- Update the package using Composer: `composer require opgginc/codezero-laravel-localized-routes`

### ➡ Added Support for Laravel 12 and PHP 8.4

This package now supports Laravel 12 and PHP 8.4.

- The dependency `codezero/laravel-uri-translator` has been replaced with `opgginc/codezero-laravel-uri-translator`.

🔸 **Actions Required**

- If you are directly using `codezero/laravel-uri-translator`, update it to `opgginc/codezero-laravel-uri-translator`.

---

## Upgrading To 4.0 From 3.x

### ➡ Minimum Requirements Updated

Due to PHP and PHPUnit version constraints with Laravel 11+, we dropped support for Laravel 7.x, 8.x and 9.x.

- The minimum PHP version required is now 8.1 (and supports PHP 8.4)
- The minimum Laravel version required is now 10 (and supports Laravel 12)

---

### ➡ Re-register Middleware

Laravel 11 no longer has a `app/Http/Kernel.php` to register middleware.
This is now handled in `bootstrap/app.php`.

🔸 **Actions Required**

If you use Laravel 11 or 12, register the middleware in `bootstrap/app.php` as described in the README.

## Upgrading To 3.0 From 2.x

This upgrade contains a number of small but breaking changes, as well as a huge internal makeover.
I listed the most important ones below.
But if you were overriding internal classes or methods, you will need to review the new source in more detail.

Over the years, this package has grown greatly in features and the codebase was getting a bit out of hand.
Therefor, I spent a lot of time refactoring every bit of code to make it more obvious what is going on, making it easier to maintain in the future.

I spent possibly even longer rewriting the README.
Hopefully, this is now a 1000 times better structured and easier to digest.

If you have any problems or improvements, you are always welcome to create an issue or pull request.

---

### ➡ Minimum Requirements Updated

We dropped support for Laravel 5.6, 5.7, 5.8 and 6.x.

- The minimum PHP version required is now 7.2.5
- The minimum Laravel version required is now 7.0

---

### ➡ Middleware Changes

Applying the `CodeZero\LocalizedRoutes\Middleware\SetLocale` middleware is now more straightforward.

The middleware is no longer automatically applied to localized routes if the `use_locale_middleware` option is set to `true`.

If you choose to use the middleware, you need to apply it manually to your routes.

🔸 **Actions Required**

- Remove the `use_locale_middleware` option from your published `config/localized-routes.php` config file.
- Remove the `use_localizer` option from your published `config/localized-routes.php` config file.
- Make sure you apply the middleware to your routes manually, either on specific routes or route groups, or by adding it to the `web` middleware group in `app/Http/Kernel.php`.
- Make sure you also add the middleware to the `$middlewarePriority` array in `app/Http/Kernel.php` in the correct spot.

```php
protected $middlewarePriority = [
    \Illuminate\Session\Middleware\StartSession::class, // <= after this
    //...
    \CodeZero\LocalizedRoutes\Middleware\SetLocale::class,
    \Illuminate\Routing\Middleware\SubstituteBindings::class, // <= before this
];
```

---

### ➡ Supported Locales, Slugs and Domains

The `supported-locales` config option has been renamed to `supported_locales`, using an underscore for consistency.

The `omit_url_prefix_for_locale` config option has been renamed to `omitted_locale`.

You can now configure your supported locales in 3 formats.

```php
// A simple array; in this case, the locales
// will be used as slugs in the URLs.
'supported_locales' => ['en', 'nl'];

// An array with locale / domain pairs, where the locale
// is used for route names etc., and the domain for the URL.
'supported_locales' => [
    'en' => 'english-domain.test',
    'nl' => 'dutch-domain.test',
];

// An array with locale / slug pairs, where the locale
// is used for route names etc., and the slug for the URL.
'supported_locales' => [
    'en' => 'english-slug',
    'nl' => 'dutch-slug',
];
```

🔸 **Actions Required**

- Remove the `custom_prefixes` option from your published `config/localized-routes.php` config file.
- Rename the `supported-locales` option to `supported_locales`
- Make sure you configure the `supported_locales` option properly if you are using custom slugs.
- Slugs can not contain dots, because then it is considered a domain.
- Rename the `omit_url_prefix_for_locale` option to `omitted_locale`

---

### ➡ Custom Route Action Changed

During route registration, we set the locale on the route using a custom route action.
We changed this route action from `laravel-localized-routes` to simply `locale`.
We also added an option to the config file in case you need to change this name.

🔸 **Actions Required**

- If you are using the `laravel-localized-routes` route action in your own code, you can either update your code with the new `locale` route action, or change it back to `laravel-localized-routes` by setting the `route_action` option in the config file.

---

### ➡ Changed `FallbackController` Namespace

The namespace of the `FallbackController` has been pluralized to `CodeZero\LocalizedRoutes\Controllers`.

🔸 **Actions Required**

- If you use the `FallbackController`, update the namespace from `CodeZero\LocalizedRoutes\Controller\FallbackController` to `CodeZero\LocalizedRoutes\Controllers\FallbackController`.

---

### ➡ Renamed `Route::localizedHas()` Method

The `Route::localizedHas()` method has been renamed to `Route::hasLocalized()` to be consistent with `Route::isLocalized()`.

🔸 **Actions Required**

- Replace any occurrence of `Route::localizedHas()` with `Route::hasLocalized()`.
