# setup-phel-action

[![GitHub Marketplace](https://img.shields.io/badge/Marketplace-setup--phel--action-blue?logo=github)](https://github.com/marketplace/actions/setup-phel)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Phel](https://img.shields.io/badge/phel-lang-purple)](https://phel-lang.org)

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

### Pin to a commit SHA (recommended for security)

Tags like `@v1` and `@main` are mutable — whoever controls this repo can move them to point at different code, and your workflow will silently run that new code with whatever permissions you grant it. Pinning to a 40-character commit SHA locks the exact code you reviewed.

<details>
<summary>How to pin to a SHA</summary>

1. Open a release on the [Releases page](https://github.com/phel-lang/setup-phel-action/releases) and copy the commit SHA it points to (or run `git ls-remote https://github.com/phel-lang/setup-phel-action v1.0.0`).
2. Replace the tag in `uses:` with that SHA. Keep a comment so humans still see the version:

   ```yaml
   # Before (mutable tag):
   - uses: phel-lang/setup-phel-action@v1

   # After (immutable SHA):
   - uses: phel-lang/setup-phel-action@a1b2c3d4e5f6789012345678901234567890abcd  # v1.0.0
   ```

</details>

<details>
<summary>How to keep pinned SHAs up to date with Dependabot</summary>

Add `.github/dependabot.yml` so Dependabot opens PRs when a new release is published:

```yaml
version: 2
updates:
  - package-ecosystem: github-actions
    directory: /
    schedule:
      interval: weekly
```

</details>

Short SHAs (7 chars) and branch names are **not** safe — only the full 40-char SHA is immutable.

### Cache Composer downloads

Speeds up CI by reusing Composer's download cache between runs:

```yaml
- uses: actions/cache@v4
  with:
    path: ~/.composer/cache
    key: composer-${{ runner.os }}-${{ hashFiles('**/composer.lock') }}
    restore-keys: composer-${{ runner.os }}-

- uses: phel-lang/setup-phel-action@v1
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

## Troubleshooting

- **`phel: command not found`** — Composer global bin dir not on `$PATH`. Action adds it automatically; if running steps in a custom shell or container, ensure `$GITHUB_PATH` is honoured.
- **`Your requirements could not be resolved... requires php >=8.4`** — bump `php-version` input to `8.4` or higher.
- **`Could not authenticate against github.com`** — Composer hit GitHub rate limit. Set `COMPOSER_AUTH` env or pass a `GITHUB_TOKEN`:
  ```yaml
  - uses: phel-lang/setup-phel-action@v1
    env:
      COMPOSER_AUTH: '{"github-oauth":{"github.com":"${{ secrets.GITHUB_TOKEN }}"}}'
  ```
- **Version mismatch** — pin `phel-version` (e.g. `^0.18`) instead of `*` for reproducible builds.

## License

MIT, see [LICENSE](LICENSE).
