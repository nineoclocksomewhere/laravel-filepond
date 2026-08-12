# Getting started

## 1. Install the package

From a Laravel application:

```bash
composer require nocs/laravel-filepond
```

The package is registered through Laravel package discovery using `Nocs\\LaravelFilepond\\LaravelFilepondServiceProvider`. If discovery is disabled, register that provider manually.

## 2. Publish configuration (optional)

```bash
php artisan vendor:publish --provider="Nocs\\LaravelFilepond\\LaravelFilepondServiceProvider" --tag=filepond
```

The provider merges the package configuration automatically. Publishing creates `config/filepond.php` so you can override it.

## 3. Configure storage

By default, uploads are stored on the `local` disk under `filepond`, which maps to `storage/app/filepond` for Laravel's standard local disk. Ensure the disk is configured and writable. To use another disk or directory:

```dotenv
FILEPOND_TEMP_DISK=local
FILEPOND_TEMP_PATH=filepond
```

These environment variables are read by the package config.

## 4. Load the client integration

The repository's `resources/js/filepond.js` expects FilePond and these plugins to be installed in the consuming application:

```bash
npm install filepond filepond-plugin-image-preview filepond-plugin-file-validate-type filepond-plugin-file-validate-size
```

Build that file through your application's normal asset pipeline. It registers the plugins, discovers `.filepond` elements, and reads optional JSON from each element's `data-filepond` attribute.

Include a CSRF meta tag if your application uses one:

```blade
<meta name="csrf-token" content="{{ csrf_token() }}">
```

## 5. Verify the routes

After the provider boots, the default effective endpoints are:

- `POST /filepond/api/process` — upload to temporary storage
- `DELETE /filepond/api/process` — delete a temporary upload

The first `/filepond` segment comes from `route_prefix`; the `api` segment is defined in `routes/web.php`. Middleware defaults to `api` and is configurable.

## First upload

A successful process response is plain text containing JSON like:

```json
{"serverId":"<encrypted-server-id>","fieldName":"avatar"}
```

Persist the returned server ID in the form submission, then resolve it server-side with `filepond()->getPathFromServerId($serverId)` or process the package's structured payload with `processFromPost()`.
