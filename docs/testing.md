# Testing and validation

## Repository inventory

At the audited `master` commit, the repository has no `tests/` directory and no `.github/workflows/` directory. There are therefore no package-provided PHPUnit/Pest tests or CI jobs to run and no repository-specific test command declared in `composer.json`.

The repository also has no `composer.lock`, `package.json`, or JavaScript lockfile. Dependency installation and JavaScript build verification require a consuming Laravel application.

## Recommended consumer checks

1. Install the package in a supported Laravel application.
2. Run `php artisan route:list` and confirm the configured process/revert routes and middleware.
3. Exercise a multipart `POST` with the configured input name; assert `200`, JSON text containing `serverId`, and a file on the configured temporary disk.
4. Exercise `DELETE` with the returned server ID; assert `200` and absence of the temporary file.
5. Submit `c`/`r`/`d` payloads through `Upload` and `processFromPost()`; verify moves, retention, uniqueness suffixes, and configured deletion behavior.
6. Build the consuming application's assets and exercise `.filepond` initialization, CSRF forwarding, process, revert, and remove controls.
7. Run the consumer's PHP formatter, static analysis, and test suite.

## Documentation-change validation

For this documentation-only change, validate:

```bash
find docs -type f -name '*.md' -print
php -r 'json_decode(file_get_contents("composer.json"), true, 512, JSON_THROW_ON_ERROR); echo "composer.json valid\n";'
```

Link targets should be checked from the repository root. Documentation intentionally calls out absent CI/tests rather than inventing a passing status.
