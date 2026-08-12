# Reference

## Configuration (`config/filepond.php`)

| Key | Default | Meaning |
| --- | --- | --- |
| `middleware` | `api` | Middleware assigned to the package route group. |
| `route_prefix` | `filepond` | Prefix added before the package routes. |
| `temporary_files_path` | `env('FILEPOND_TEMP_PATH', 'filepond')` | Directory used for temporary uploads. |
| `temporary_files_disk` | `env('FILEPOND_TEMP_DISK', 'local')` | Filesystem disk used for temporary uploads and deletion. |
| `input_name` | `*` | Request file key read by the upload controller. |

## Routes and HTTP contract

`routes/web.php` defines these routes inside an `api` prefix; the service provider wraps them in `config('filepond.route_prefix')`:

| Method | Effective path by default | Behavior |
| --- | --- | --- |
| `POST` | `/filepond/api/process` | Reads the configured file input and optional `fieldName`, stores the upload, and returns JSON text with `serverId` and `fieldName`. Missing input returns `422`; a failed store returns `500`. |
| `DELETE` | `/filepond/api/process` | Reads the encrypted server ID from the raw body and deletes the corresponding temporary file. Returns `200` on deletion and `500` when deletion returns false. |

## Public PHP API

### `filepond()` helper

Returns the container-resolved `Nocs\LaravelFilepond\Filepond` service.

### `Filepond`

- `getServerIdFromPath($path): string` — encrypts an absolute path with Laravel `Crypt`.
- `getPathFromServerId($serverId): string` — decrypts, rejects blank IDs, and enforces the configured temporary base path.
- `getRelativePathFromServerId($serverId): string` — resolves a server ID and strips the standard `storage/app/` prefix.
- `getBasePath(): string` — returns the configured disk's base path plus temporary directory.
- `pathToToken(string $path): string` — deterministic UUID-shaped MD5 token of the storage-relative path.
- `forceRelative(string $path): string` — removes the standard local `storage/app` prefix.
- `getStoreDataFromPath($path): object|null` — returns token, filename, path, size, and MIME type; image files also include dimensions and centered focus coordinates.
- `publishUpload($tmpPath, $dirName = 'uploads'): object|false` — moves a file to the public storage layout and returns metadata.
- `getFileDataFromStoredByToken(array $stored, string $token): object|null` — finds metadata in mixed array/object stored data.
- `unmask(array $postData, $storedData): array` — converts new server IDs to metadata and matches retained/deleted entries against stored metadata.
- `uniquePath(string $path): string` — finds a non-existing storage path by adding numeric suffixes.
- `process(array $data, array $options = []): array` — applies deletes, retains old files, and moves creates. Options are `path` (default `uploads`) and `delete` (default `false`).
- `processFromPost($posted, $current, $path = 'uploads'): array` — normalizes posted data, unmasks it, and processes it.

### `Rules\Upload`

An `Illuminate\Contracts\Validation\Rule` implementation. Constructor options are `min`, `max`, `limitFileSize` (bytes), and `limitToMimetypes` (array). It validates the normalized active set and, for new uploads, size and MIME restrictions.

### Exceptions

`InvalidPathException` extends `InvalidArgumentException`, implements `LaravelFilepondException`, defaults to message `The given file path was invalid`, and uses code `400`.
