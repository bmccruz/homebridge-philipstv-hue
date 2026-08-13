# AGENTS.md

Instructions for AI coding agents working in this repository.

## What this is

`homebridge-philipstv-hue-2020` is a Homebridge **accessory** plugin (legacy `registerAccessory` API, not a dynamic platform) that exposes a Philips TV with the [JointSpace](http://jointspace.sourceforge.net/) API enabled as a HomeKit Television, with optional Ambilight and Ambilight+Hue control. Plain CommonJS JavaScript, single file, no build step. It's a long-running fork chain (see README.md's Description section for the history) — expect some inconsistent style left over from earlier authors.

## Layout

```
index.js              # the entire plugin: accessory registration, JointSpace HTTP client, HomeKit services
config.schema.json     # Homebridge UI X config form (pluginType: accessory)
.github/workflows/     # ci.yaml (push/PR), gitleaks.yaml (push/PR), publish-to-npm.yaml (tag push), renovate.yaml (cron)
```

There is no `src/`, no TypeScript, and no test suite — this predates those conventions and hasn't been migrated. Don't introduce a build step or bundler as a side effect of an unrelated change.

## Build, test, lint

```bash
npm ci
node --check index.js   # syntax check — closest thing to a test this repo has
```

There's no `npm test`. If you add real test coverage, wire it into `package.json`'s `scripts.test` and `.github/workflows/ci.yaml` together, not just one of the two.

To exercise a change against a real TV, symlink this repo into a local Homebridge's plugin directory (or use `homebridge -P <path-to-this-repo's-parent>`) and add a `PhilipsTV` accessory to `config.json` — see README.md for the config shape.

## Conventions

- Match the existing style: tabs for indentation, `var`, string concatenation over template literals. This file is a single accumulated artifact from several authors — don't do a drive-by reformat of unrelated code while fixing something else.
- No comments explaining *what* code does — only *why*, and only when genuinely non-obvious. Several existing comments violate this; don't add more of them.
- Don't add abstractions, config flags, or generality beyond what's actually needed. This plugin already carries some incidental complexity (per-model-year API version switching, dual `wol_url`/`wol_urls` config) — don't add a third way to do the same thing.
- Any new user-facing config key needs a matching entry in `config.schema.json` *and* the README's config examples — the plugin reads `config.json` directly with no schema validation at runtime, so an undocumented key is effectively invisible to users of Homebridge UI X.
- `request` (the HTTP client dependency) is deprecated and unmaintained upstream. Don't add new features that lean on it further; a replacement (e.g. native `fetch`, since the `engines.node` floor now supports it) is a reasonable follow-up but is a large, separate change — don't fold it into an unrelated fix.

## Release process

Maintainer-only: push a `vX.Y.Z` tag from `master` (matching `package.json`'s `version`) to trigger `.github/workflows/publish-to-npm.yaml`, which publishes to npm via Trusted Publishing (GitHub Actions OIDC, no stored token). See CONTRIBUTING.md.

## Commit/PR conventions

Normal open-source practice: your own name/email as author, clear commit messages, one logical change per PR. See CONTRIBUTING.md.
