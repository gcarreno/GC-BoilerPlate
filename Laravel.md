# Laravel

These instruction are based on version 10.41.0.

## Creating

```console
$ composer create-project laravel/laravel test
```

## With Breeze Started pack

### First Migration

```console
$ ./artisan migrate
```

### Install Authentication

```console
$ composer require laravel/breeze --dev

$ ./artisan breeze:install
```

### Install Authorisation

**Note**: TODO

## With Voyager Started Pack

This starter pack is based in `Vue` and `Bootstrap`.

### Installing Voyager

```console
$ composer require tcg/voyager

$ ./artisan voyager:install
```
### Creating Admin User

```console
$ ./artisan voyager:admin your@email.com --create
```
