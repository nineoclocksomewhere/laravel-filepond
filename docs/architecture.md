# Architecture

## Package boot sequence

1. Laravel discovers `LaravelFilepondServiceProvider` from `composer.json`.
2. `register()` merges `config/filepond.php` into the application's `filepond` config.
3. `boot()` registers the package route group and exposes the `filepond` publish tag.
4. `routes/web.php` loads the controller routes beneath the configured prefix.
5. The global `filepond()` helper resolves `Filepond` from the application container.

## Upload data flow

```text
FilePond client
  -> POST /filepond/api/process (multipart file + fieldName)
  -> FilepondController::upload
  -> configured filesystem/{temporary path}/{random directory}/{original name}
  -> Crypt::encryptString(absolute path)
  -> plain-text JSON { serverId, fieldName }
```

Revert follows the reverse trust boundary: the client sends the server ID as the request body, `Filepond::getPathFromServerId()` decrypts it, confirms it begins with the configured temporary base path, and the controller deletes it on the configured disk.

## Form-processing model

The browser-side helper records server IDs in `c`. Application code can preserve existing metadata in `r` and mark removed entries in `d`. `unmask()` resolves `c` through encrypted paths and resolves `r`/`d` through metadata tokens. `process()` then deletes requested entries when configured, retains old entries, and moves new files to a destination path.

## Storage and security boundaries

Server IDs are encrypted paths, not database IDs. This prevents the normal client response from exposing the path directly, but callers must still validate authorization before accepting a server ID in a business operation. Path resolution is constrained to `getBasePath()`; application code should also scope the resulting file to the current user/model. `process()` defaults to not physically deleting files (`delete: false`), so choose deletion behavior deliberately.

## Repository surface inventory

- PHP package source in `src/`
- Laravel route definition in `routes/web.php`
- Published config in `config/filepond.php`
- Optional browser helper in `resources/js/filepond.js`
- Global helper in `helpers/helpers.php`
- No HTTP client collection, Bruno collection, domain events, queued jobs, or webhooks are implemented in this repository.
