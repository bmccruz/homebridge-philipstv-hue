# Contributing

Thanks for considering a contribution to `homebridge-philipstv-hue-2020`.

## Dev setup

```bash
git clone https://github.com/rafaelgaspar/homebridge-philipstv-hue-2020
cd homebridge-philipstv-hue-2020
npm ci
node --check index.js
```

There's no build step — it's plain CommonJS. To try a change against a real TV, either:

- symlink the repo into Homebridge's plugin directory (e.g. `npm link` from this repo, then `npm link homebridge-philipstv-hue-2020` from your Homebridge install), or
- run `homebridge -P <path to this repo's parent directory>`

and add a `PhilipsTV` accessory to your Homebridge `config.json` — see the README for the config shape.

## Code style

- Plain CommonJS (`require`/`module.exports`), tabs for indentation. Match the existing style in `index.js` rather than introducing a new pattern (e.g. don't switch to `let`/`const`/template literals in code you're touching just because it's an unrelated improvement).
- No comments explaining *what* code does; only *why*, when it's genuinely non-obvious.
- Any new user-facing config key needs an entry in `config.schema.json` and the README's config examples.

## Workflow

1. Fork the repo and branch off `master`.
2. Keep PRs scoped to one logical change — avoid bundling unrelated fixes/features.
3. Run `node --check index.js` before opening the PR (there's no other automated test yet). CI runs the same check and must be green before merge.
4. Update the README/config.schema.json alongside any user-facing change (new config option, new supported TV model/year, etc.).

## Releasing (maintainers)

Releases are cut by pushing a `vX.Y.Z` tag from `master` (matching `package.json`'s `version`). That triggers `.github/workflows/publish-to-npm.yaml`, which publishes to npm.

### npm publishing auth

The publish job authenticates via [Trusted Publishing](https://docs.npmjs.com/trusted-publishers/) (GitHub Actions OIDC) — configured on the package's npmjs.com settings page (Trusted Publisher: org `rafaelgaspar`, repo `homebridge-philipstv-hue-2020`, workflow filename `publish-to-npm.yaml`). No stored token; the old `NPM_TOKEN` repo secret has been deleted.

## Dependency updates

[Renovate](https://docs.renovatebot.com/) is configured via `renovate.json` (npm dependencies, GitHub Actions pins — grouped by non-major/major, non-majors automerge as soon as CI passes). It runs self-hosted via `.github/workflows/renovate.yaml` (weekday cron + manual dispatch) using [`renovatebot/github-action`](https://github.com/renovatebot/github-action), authenticating as a GitHub App rather than the hosted [Renovate GitHub App](https://github.com/apps/renovate). The workflow mints a short-lived installation token via [`actions/create-github-app-token`](https://github.com/actions/create-github-app-token) from the `RENOVATE_APP_CLIENT_ID` and `RENOVATE_APP_PRIVATE_KEY` repo secrets — a maintainer needs to have created a GitHub App, installed it on this repo, and populated those secrets for the workflow to run.
