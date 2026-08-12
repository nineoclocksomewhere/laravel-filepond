# Compatibility

## Declared package compatibility

The current `composer.json` declares:

- PHP `^7.0 | ^8.0`
- `illuminate/contracts`, `illuminate/http`, `illuminate/routing`, and `illuminate/support`: Laravel `5.5`–`5.8` plus `^6.0` through `^13.0`

The package is tagged MIT and is published as `nocs/laravel-filepond`.

## Integration assumptions

- Laravel package discovery or manual service-provider registration is available.
- The application provides Laravel `Crypt`, filesystem, routing, config, validation, and translation services used by the package.
- The default temporary disk is the Laravel `local` disk and the default temporary directory is `storage/app/filepond`.
- The bundled JavaScript helper assumes a browser build with FilePond and its three imported plugins.
- The helper assumes a CSRF meta tag when CSRF forwarding is required.

## Scope and limitations

This repository does not declare a JavaScript package manifest, so npm dependency versions are not pinned here. It also contains no automated test suite, CI workflow, HTTP/Bruno collection, events, queue jobs, or webhooks. Compatibility claims above are Composer constraints, not a matrix verified by this repository's tests.

The source still imports `Nocs\\Filepond\\Support\\Filepond` in the service provider without declaring that package in `composer.json`; the import is unused, but downstream consumers should treat it as a source-level dependency smell rather than assume an additional package is installed.
