---
type: reference
title: "errors"
tags: [type/reference]
created: 2026-06-11
updated: 2026-06-11
---

# Errors

This project does not emit a custom CLI with defined exit codes. The errors below are the runtime failures observable during local development and build.

## Exit code table

| Code | Meaning | Trigger | Fix |
|---|---|---|---|
| 0 | Success | Normal Jekyll build or serve completion | — |
| 1 | Jekyll build error | Liquid syntax error in a template, YAML parse failure in `_data/data.yml` or `_config.yml`, missing include file | Check Jekyll log output for the offending file/line; validate YAML with `ruby -e "require 'yaml'; YAML.load_file('_data/data.yml'); puts 'OK'"` |
| 1 | Bundler error | Missing gem, incompatible gem version, corrupt `Gemfile.lock` | Run `bundle install`; if lock is corrupt, delete `Gemfile.lock` and re-run |
| non-0 | Docker Compose failure | Image pull failure, port 4000 already bound, volume mount path invalid | Check `docker compose logs`; ensure port 4000 is free and the repo path is correctly mounted |

## Error messages

| Message | Code | Notes |
|---|---|---|
| `Liquid Exception: ... in ...` | 1 | Liquid template syntax error — check the named include/layout file |
| `YAML Exception reading _data/data.yml: ...` | 1 | Malformed YAML in the primary data file — run the `ruby` validation command |
| `Error: No such file or directory @ rb_sysopen - _data/data.yml` | 1 | `data.yml` deleted or renamed — restore from git |
| `Address already in use - bind(2) for 0.0.0.0:4000` | 1 | Port 4000 is occupied by another process — kill the conflicting process or change port with `--port` |
| `Unable to find gem ... in any of the sources` | 1 | Bundler can't resolve a gem — run `bundle install` |
| `Server address: http://0.0.0.0:4000` | 0 | Not an error — this confirms successful server start |

## Notes

- **No automated test suite exists.** Verification is manual — see [dev-loop.md](dev-loop.md) for the full manual check list.
- The `compress.html` layout minifies HTML output; if the site renders blank, confirm that `layout: compress` in `_layouts/default.html` is not causing a parse error in the minified output.
