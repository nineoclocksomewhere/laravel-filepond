# Usage

## Upload and revert

The bundled JavaScript config posts each file as multipart form data. It sends `fieldName` plus a file under the field's name, and adds `_token` when a CSRF meta tag is present. The controller stores the file under the configured temporary path in a random subdirectory, preserving the client original filename.

FilePond receives an encrypted `serverId`. Revert/delete sends that ID as the raw request body. The controller decrypts and validates the path before deleting it.

## Resolve a server ID

```php
use Nocs\LaravelFilepond\Filepond;

$filepond = app(Filepond::class);
$absolutePath = $filepond->getPathFromServerId($request->input('avatar'));
$relativePath = $filepond->getRelativePathFromServerId($request->input('avatar'));
```

`getPathFromServerId()` rejects an empty ID and paths outside the configured temporary base path with `InvalidPathException`. Decryption failures are not converted by this class, so handle invalid user input at the application boundary.

## Process a FilePond form payload

The package models submitted file state with three arrays:

- `c` — newly created uploads, represented by server IDs
- `r` — existing/stored files, represented by their stored metadata tokens
- `d` — deleted existing files, represented by tokens

The `Upload` rule and `processFromPost()` expect all three keys to be arrays. A typical controller flow is:

```php
use Nocs\LaravelFilepond\Rules\Upload;

$request->validate([
    'attachments' => [new Upload(['min' => 1, 'max' => 5])],
]);

$files = filepond()->processFromPost(
    $request->input('attachments'),
    $model->attachments,
    'uploads/attachments'
);

$model->attachments = $files;
$model->save();
```

`processFromPost()` accepts a JSON string, object, or array for the posted payload. It unmasks new server IDs, removes deleted entries from the active set, and moves newly created files to the requested path. Existing entries are retained. Duplicate destination names are made unique (`name.1.ext`, then `name.2.ext`, and so on).

## Publish a temporary upload directly

For a single temporary path:

```php
$metadata = filepond()->publishUpload($temporaryPath, 'uploads');
```

This moves the file to `public/{dir}/{session-id-prefix}/{path-hash-prefix}/{basename}` and returns metadata, or `false` when the move fails. The method uses Laravel's default storage facade disk behavior; use `processFromPost()` when you need FilePond's `c`/`r`/`d` lifecycle.

## Validate uploads

```php
new \Nocs\LaravelFilepond\Rules\Upload([
    'min' => 1,
    'max' => 10,
    'limitFileSize' => 5 * 1024 * 1024,
    'limitToMimetypes' => ['image/jpeg', 'image/png'],
]);
```

`min` defaults to `1`; `max` is unlimited by default. Size is compared in bytes. Error messages use translation keys `validation.upload`, `upload-min`, `upload-max`, `upload-limit-file-size`, and `upload-limit-mimetypes`; define these keys in the consuming application's language files for customized text.

## JavaScript conventions

The bundled helper initializes every `.filepond` element, registers image preview/type/size plugins, and exposes `window.ponds` and `window.pondMappings`. For an element with `data-filepond`, the attribute must contain valid JSON or default FilePond options are used. The helper also supports `.file-uploads .file-upload a.remove` controls that append a token to the hidden payload's `d` array.
