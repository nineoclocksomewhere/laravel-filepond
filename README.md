# Laravel FilePond Backend

Laravel backend integration for [FilePond](https://pqina.nl/filepond/). The package provides temporary uploads, encrypted FilePond server IDs, revert/delete handling, file metadata helpers, and a validation rule for FilePond's `c`/`r`/`d` payload.

> This documentation is derived from the current source at `master` and describes the package as implemented. It does not imply support for APIs that are not present in this repository.

## Documentation

- [Getting started](docs/getting-started.md) — install, publish configuration, and connect FilePond.
- [Usage](docs/usage.md) — upload lifecycle, processing submitted files, validation, and publishing.
- [Reference](docs/reference.md) — configuration, routes, classes, methods, and response contracts.
- [Architecture](docs/architecture.md) — service-provider bootstrapping and data flow.
- [Compatibility](docs/compatibility.md) — declared PHP/Laravel versions and integration boundaries.
- [Testing](docs/testing.md) — repository test inventory and validation guidance.

## Requirements

- PHP `^7.0 | ^8.0`
- Laravel components `illuminate/contracts`, `http`, `routing`, and `support` versions `5.5` through `13.x` as declared in `composer.json`
- A Laravel application with encryption, filesystem, routing, and session services configured as required by the APIs you use

## Quick start

```bash
composer require nocs/laravel-filepond
php artisan vendor:publish --provider="Nocs\\LaravelFilepond\\LaravelFilepondServiceProvider" --tag=filepond
```

The provider auto-registers the package routes. The default client integration in `resources/js/filepond.js` uses `/filepond/api/process` for processing and sends the Laravel CSRF token when a `meta[name="csrf-token"]` element exists. See the [getting-started guide](docs/getting-started.md) before configuring a custom client.

## License

MIT. See [LICENSE](LICENSE).
