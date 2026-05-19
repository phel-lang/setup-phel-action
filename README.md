# setup-phel-action

GitHub Action to install [Phel](https://phel-lang.org), a functional Lisp dialect that compiles to PHP, in your workflows.

## Usage

```yaml
name: CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: phel-lang/setup-phel-action@v1
        with:
          phel-version: '*'   # optional, default '*'
          php-version: '8.4'  # optional, default '8.4'

      - run: phel test
```

## Inputs

| Name | Default | Description |
|------|---------|-------------|
| `phel-version` | `*` | Phel version constraint passed to `composer require` |
| `php-version` | `8.4` | PHP version to install (Phel requires >= 8.4) |
| `tools` | `composer:v2` | Extra tools passed to `shivammathur/setup-php` |

## What it does

1. Installs PHP via [`shivammathur/setup-php`](https://github.com/shivammathur/setup-php).
2. Installs `phel-lang/phel-lang` globally with Composer.
3. Adds the Composer global bin directory to `$PATH` so `phel` is callable.
4. Runs `phel --version` to verify the install.

## License

MIT, see [LICENSE](LICENSE).
