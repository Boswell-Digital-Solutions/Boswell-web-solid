# Known Issues

## 2026-10-02 - The code workflows watch `main`, but the default branch is `master` (open)

- What is wrong: `tests.yml`, `codeql.yml` and `format.yml` trigger on `push` and `pull_request` for branch `main`. The default branch is `master`. These workflows do not start for pushes to `master` or pull requests into `master`.
- Root cause: workflow files were written for a `main` branch that this repo does not use.
- Effect: the build, test, lint, CodeQL and Format checks do not run on normal changes. The CodeQL weekly `schedule` still runs on the default branch.
- Fix: change `branches: [main]` to `[master]` in the three workflows, then confirm that they pass. This was not changed here, because enabling them could turn on checks that are red today.
- The new `documentation.yml` uses `master`.
- Scope: open.

## 2026-10-02 - The workflows use retired action versions (open)

- `actions/checkout@v3`, `actions/setup-node@v3`, `github/codeql-action/*@v1` and `advanced-security/filter-sarif@main`. `actionlint` reports the runners as too old. CodeQL v1 is retired and is expected to fail when it runs.
- Scope: open.

## 2026-10-02 - `format.yml` commits to `main` from CI (open)

- `format.yml` runs `prettier --write .` and auto-commits to `main` with `contents: write`. Prettier 3.0.0 reports style issues in 13 files under `docs/` today.
- Scope: open. The owner must decide whether an auto-commit job is wanted.

## 2026-10-02 - A documentation-only push may redeploy the Render service (open)

- `render.yaml` sets no `autoDeployTrigger` and no `buildFilter`. Render deploys on each push to the watched branch by default. A GitHub Actions `paths` filter cannot stop this.
- Not verified: the Render dashboard setting. The deploy does not read Markdown.
- Mitigation: `render.yaml` now sets `buildFilter.ignoredPaths` for `docs/**`, `doc/**`, `*.md` and `**/*.md`. A push that changes only those paths must not build. Any other path still builds.
- Not observed: no documentation-only push has run since the change. Not verified: the Render service may have been made by hand in the dashboard, and then it ignores `render.yaml`. The owner must confirm that the Blueprint manages the service, or set the same filter in the dashboard.
- Scope: mitigated in `render.yaml`, not yet observed. Low severity.
