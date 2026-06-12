---
type: reference
title: "cli"
tags: [type/reference]
created: 2026-06-11
updated: 2026-06-11
---

# CLI Reference

This project has no installable CLI binary. Interaction is through Docker Compose and standard Jekyll/Ruby commands. All commands below are run from the repo root.

## Commands

### `docker compose up`

Start the local development server with live reload.

**Usage:**
```
docker compose up
```

**Flags:** None — flags are baked into `docker-compose.yml` (`--watch`, `--force_polling`, `--verbose`, `--livereload`, `--host 0.0.0.0`).

**Expected output:** `Server address: http://0.0.0.0:4000` in container log.

**Notes:**
- Uses `jekyll/builder:4.0` image.
- `--force_polling` is mandatory on Windows bind mounts; do not remove it.
- Watches `_data/data.yml`, `_includes/`, `_layouts/`, `_sass/` for changes and hot-reloads.

**Exit codes:** see [errors.md](errors.md)

---

### `bundle exec jekyll serve`

Start a local development server without Docker.

**Usage:**
```
bundle exec jekyll serve
```

**Flags:**

| Flag | Type | Default | Description |
|---|---|---|---|
| `--watch` | bool | false | Watch for file changes and rebuild |
| `--livereload` | bool | false | Inject LiveReload script for auto-refresh |
| `--host` | string | `127.0.0.1` | Bind address; use `0.0.0.0` to expose on LAN |
| `--port` | int | `4000` | HTTP port |
| `--force_polling` | bool | false | Use polling watcher instead of inotify (required on Windows) |

**Exit codes:** see [errors.md](errors.md)

---

### `bundle exec jekyll build`

Build the site to `_site/` without serving.

**Usage:**
```
bundle exec jekyll build
```

**Flags:**

| Flag | Type | Default | Description |
|---|---|---|---|
| `--destination` | path | `_site/` | Output directory |
| `--baseurl` | string | `` | Override site base URL |

**Exit codes:** see [errors.md](errors.md)

---

### `ruby -e "require 'yaml'; YAML.load_file('_data/data.yml'); puts 'OK'"`

Validate the primary YAML data file without starting Jekyll.

**Usage:**
```bash
ruby -e "require 'yaml'; YAML.load_file('_data/data.yml'); puts 'OK'"
```

**Expected output:** `OK` on stdout.

**Notes:** Prints a YAML parse error and exits non-zero if `_data/data.yml` is malformed. Run this before committing any YAML changes.

**Exit codes:** see [errors.md](errors.md)

---

### `bundle install`

Install Ruby gem dependencies.

**Usage:**
```
bundle install
```

**Notes:** Reads `Gemfile` and `Gemfile.lock`. Run once after cloning or after `Gemfile` changes.

**Exit codes:** see [errors.md](errors.md)
