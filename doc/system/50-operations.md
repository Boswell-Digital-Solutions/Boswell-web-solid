# Operations

**Document version:** 1.0 (bootstrap scaffold)

Deployment, observability, and bounded operator repair.

> This chapter is a registry-generated bootstrap scaffold for a
> `service` class documentation system. Replace this placeholder with
> real authored content. Registry will not invent repo truth that is not
> already present in the repo.

## Which CI runs for which change

A change that touches only documentation runs the Documentation CI and no code CI.
A change that touches any other file runs the code CI.
A change that touches both runs both.
The code CI runs only when code changes.

These workflows have a workflow-level `paths` filter on `push` and `pull_request`: `tests.yml` (`Build and Test`), `codeql.yml` (`CodeQL`) and `format.yml` (`Format`, push only).
The filter includes `**` and then excludes `docs/**`, `doc/**` and `**/*.md`.
The last matching pattern wins.
A change to `.github/workflows/**` is code, so it runs these workflows.
CodeQL on a documentation-only change analyzes no code, so the filter skips it.
`format.yml` is an auto-fix job. It runs `prettier --write .` and commits the result to `main`. It is not a check.
It formats Markdown too, so the next code push formats any Markdown that a documentation-only push left unformatted.

The site does not build from Markdown.
The site is SolidJS code under `dev/` and a Node server under `server/`.
No test, script or build step reads a documentation file, so no documentation path is re-included.
A Markdown change publishes no content.

The `Documentation CI` workflow (`.github/workflows/documentation.yml`) runs for `docs/**`, `doc/**`, `**/*.md` and its own file.
It runs `bash doc/system/BUILD.sh` and fails if `git diff --exit-code -- doc` shows a difference.

The weekly schedule in `codeql.yml` is not affected by the filter.
The three code workflows watch branch `main`. The default branch is `master`. See `docs/KNOWN_ISSUES.md`.
The repo has no secret scanner workflow today.
A secret scanner must run on every change, because a documentation file can hold a secret.

Render deploys the service from `render.yaml`.
Render starts a deploy on each push to the branch it watches. This is Render configuration, not a GitHub workflow.
The path filter does not stop it.

Do not add a required status check on a path-filtered workflow.
When the filter skips the workflow, the required check stays pending and blocks the merge.

