![pexels-photo-325229](https://user-images.githubusercontent.com/1791050/30768358-0df9d0f2-9fbb-11e7-9f10-ad40b83bbf59.jpg)

# Model Caching for Laravel
[![Gitter](https://badges.gitter.im/GeneaLabs/laravel-model-caching.svg)](https://gitter.im/GeneaLabs/laravel-model-caching?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=body_badge)
[![Travis](https://img.shields.io/travis/GeneaLabs/laravel-model-caching/master.svg)](https://travis-ci.org/GeneaLabs/laravel-model-caching)
[![Scrutinizer](https://img.shields.io/scrutinizer/g/GeneaLabs/laravel-model-caching/master.svg)](https://scrutinizer-ci.com/g/GeneaLabs/laravel-model-caching)
[![Coveralls](https://img.shields.io/coveralls/GeneaLabs/laravel-model-caching/master.svg)](https://coveralls.io/github/GeneaLabs/laravel-model-caching)
[![GitHub (pre-)release](https://img.shields.io/github/release/GeneaLabs/laravel-model-caching/all.svg)](https://github.com/GeneaLabs/laravel-model-caching)
[![Packagist](https://img.shields.io/packagist/dt/GeneaLabs/laravel-model-caching.svg)](https://packagist.org/packages/genealabs/laravel-model-caching)
[![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg)](https://raw.githubusercontent.com/GeneaLabs/laravel-model-caching/master/LICENSE)

## Impetus
I created this package in response to a client project that had complex, nested
forms with many `<select>`'s that resulted in over 700 database queries on one
page. I needed a package that abstracted the caching process out of the model
for me, and one that would let me cache custom queries, as well as cache model
relationships. This package is the attempt to address those requirements.

## Features
-   automatic, self-invalidating relationship (both eager- and lazy-loaded) caching.
-   automatic, self-invalidating model query caching.
-   automatic use of cache tags for cache providers that support them (will
    flush entire cache for providers that don't).

## Requirements
-   PHP >= 7.1.3
-   Laravel >= 5.5

## Installation
```
composer require genealabs/laravel-model-caching
```

## Configuration
### Optional Custom Cache Store
If you would like to use a different cache store than the default one used by
your Laravel application, you may do so by setting the `MODEL_CACHE_STORE`
environment variable in your `.env` file to the name of a cache store configured
in `config/cache.php` (you can define any custom cache store base on your
specific needs there). For example:
```
MODEL_CACHE_STORE=redis
```

## Usage
For best performance a taggable cache provider is recommended (redis,
memcached). While this is optional, using a non-taggable cache provider will
mean that the entire cache is cleared each time a model is created, saved,
updated, or deleted.

For ease of maintenance, I would recommend adding a `BaseModel` model that
uses `Cachable`, from which all your other models are extended. If you
don't want to do that, simply extend your models directly from `CachedModel`.

Here's an example `BaseModel` class:

```php
<?php namespace App;

use GeneaLabs\LaravelModelCaching\Traits\Cachable;

abstract class BaseModel
{
    use Cachable;
    //
}
```

### Exception: User Model
I would not recommend caching the user model, as it is a special case, since it
extends `Illuminate\Foundation\Auth\User`. Overriding that would break functionality.
Not only that, but it probably isn't a good idea to cache the user model anyway,
since you always want to pull the most up-to-date info on it.

### Optional Disabling Caching of Queries
**Recommendation: add this to all your seeder queries to avoid pulling in
cached information when reseeding multiple times.**
You can disable a given query by using `disableCache()` in the query chain, and
it needs to be placed (anywhere) prior to the query command (`get()`, `all()`,
`find()`, etc). For example:
```php
$results = $myModel->disableCache()->all();
```

### Manual Flushing of Specific Model
You can flush the cache of a specific model using the following artisan command:
```sh
php artisan modelCaching:flush --model=App\Model
```

This comes in handy when manually making updates to the database. You could also
trigger this after making updates to the database from sources outside your
Laravel app.

## Summary
**That's all you need to do. All model queries and relationships are now
cached!**

In testing this has optimized performance on some pages up to 900%! Most often
you should see somewhere around 100% performance increase.

## Commitment to Quality
During package development I try as best as possible to embrace good design and
development practices to try to ensure that this package is as good as it can
be. My checklist for package development includes:

-   ✅ Achieve as close to 100% code coverage as possible using unit tests.
-   ✅ Eliminate any issues identified by SensioLabs Insight and Scrutinizer.
-   ✅ Be fully PSR1, PSR2, and PSR4 compliant.
-   ✅ Include comprehensive documentation in README.md.
-   ✅ Provide an up-to-date CHANGELOG.md which adheres to the format outlined
    at <http://keepachangelog.com>.
-   ✅ Have no PHPMD or PHPCS warnings throughout all code.

## Contributing
Please observe and respect all aspects of the included Code of Conduct <https://github.com/GeneaLabs/laravel-model-caching/blob/master/CODE_OF_CONDUCT.md>.

### Reporting Issues
When reporting issues, please fill out the included template as completely as
possible. Incomplete issues may be ignored or closed if there is not enough
information included to be actionable.

### Submitting Pull Requests
Please review the Contribution Guidelines <https://github.com/GeneaLabs/laravel-model-caching/blob/master/CONTRIBUTING.md>.
Only PRs that meet all criterium will be accepted.

## ❤️ Open-Source Software - Give ⭐️
We have included the awesome `symfony/thanks` composer package as a dev
dependency. Let your OS package maintainers know you appreciate them by starring
the packages you use. Simply run composer thanks after installing this package.
(And not to worry, since it's a dev-dependency it won't be installed in your
live environment.)
