# tessera-android

Android app (Trusted Web Activity) for Tessera, a fast private Matrix client

## Commands

<!-- TODO: build, test, lint, type-check/vet, run. Keep them one-liners. -->

Run the tests, linter and type/vet checks before committing; CI runs them on every push.

## Layout

<!-- TODO: one line per important directory/file. Keep the entry point thin; put logic in small modules that tests can import. -->

## Conventions and gotchas

<!-- TODO: add each non-obvious thing that bit you, with the WHY. Start with these transferable lessons: -->

- **Security tests fail without the fix.** Every auth/injection/resource-limit fix ships with a regression test that was seen failing first.
- **Verify behaviour end to end, not just in unit tests.** Several real bugs here (an auth bypass, inverted flag defaults, an error message split across chunks) passed the unit tests; they surfaced only when running the real binary/server and a browser.
- **Library upgrades change semantics silently.** After a major bump, run the real program and compare behaviour, not just the test suite.
- **Don't trust a helper's name.** E.g. a "signed cookie" parser returns unsigned values unchanged; read what a function does for the *invalid* case.
- Keep dependencies minimal; prefer a small in-repo helper over a new package.

## Security checks and GitHub config

- Explicit CodeQL (`.github/workflows/codeql.yml` + `.github/codeql/codeql-config.yml`: the project language plus the Actions workflows, `security-extended`). GitHub's *default setup* for code scanning must be switched off, because advanced and default setup can't coexist.
- Trivy (`.github/workflows/trivy.yml`, `.trivyignore.yaml`): exceptions need a written reason.
- OpenSSF Scorecard (`.github/workflows/scorecard.yml`). Process findings that don't apply to a solo-maintained repo (code review, fuzzing, best-practices badge) are dismissed with a comment, not left open.
- Every third-party GitHub Action is pinned to a **commit SHA** (version in a comment) and the repo enforces it (`sha_pinning_required`); Dependabot keeps them current. Workflow tokens are read-only by default; give a job write access only if it needs it, with a comment saying why.
- `SECURITY.md` policy; GitHub private vulnerability reporting enabled; issue and PR templates in `.github/`.
- **Always check open Dependabot and code-scanning alerts before and after releases:**

  ```sh
  gh api repos/hbenali/tessera-android/dependabot/alerts -q '[.[]|select(.state=="open")]|length'
  gh api 'repos/hbenali/tessera-android/code-scanning/alerts?state=open' -q length
  ```

## Releasing

See `RELEASING.md`. Summary: signed tag (`git tag -s`); the tag workflow builds everything and creates a **draft** release (releases are immutable once published, so assets can't be added later); then publish it with notes: `gh release edit X.Y --title X.Y --notes-file notes.md --draft=false`.

Lessons: don't remove `contents: write` from a workflow job that pushes commits (a "least privilege" cleanup broke a release step); a workflow change that only runs on tags can't be tested without releasing, so keep such changes small and re-check them on the next release.

## Docs

`docs/wiki/` is the source of truth for the GitHub wiki (published by `.github/workflows/wiki.yml` on every change; edits made in the wiki UI are overwritten). GitHub only creates the wiki repository when its first page is created once in the web UI.
