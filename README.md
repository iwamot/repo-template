# repo-template

Starter template for repositories in iwamot's ecosystem.

## Files

| Path | Purpose |
|------|---------|
| `.github/Oidefile` | Manifest of files this template distributes. `oide.yml` pulls every listed path into derived repos. |
| `.github/release.yml` | Categories for GitHub's auto-generated release notes, keyed on the labels `auto-label.yml` sets. |
| `.github/renovate.json` | Extends the `iwamot/renovate-config` preset. |
| `.github/workflows/auto-label.yml` | Labels PRs from their Conventional Commit title. |
| `.github/workflows/dco.yml` | Checks that every PR commit carries a DCO sign-off. |
| `.github/workflows/dependabot-auto-merge.yml` | Auto-merges Dependabot PRs. |
| `.github/workflows/dependency-review.yml` | Vulnerability and license review on PRs. |
| `.github/workflows/oide.yml` | Pulls the files listed in `.github/Oidefile` from this template. See [Staying in sync](#staying-in-sync). |
| `.github/workflows/release.yml` | Creates a GitHub Release when a `v*.*.*` tag is pushed, or from a manual run given the tag to create. |
| `.github/workflows/renovate.yml` | Self-hosted Renovate runner (twice a day + on push to main). |
| `.github/workflows/validate.yml` | Runs `validate.sh` via `iwamot/workflows` on push, on PR, and daily, so a new advisory fails the audit even when nothing changes. |
| `CONTRIBUTING.md` | Contribution guide: local setup, DCO, and Conventional Commits. |
| `LICENSE` | Project license. |
| `SECURITY.md` | Minimal security policy. Directs vulnerability reports to GitHub Security Advisories. |
| `mise.toml` | Pins mise minimum version and includes shared tasks from `iwamot/mise-tasks`. |
| `validate.sh` | Lint entry point invoked by `iwamot/actions/mise-validate`. Add repo-specific lint at the marked location. |

## Staying in sync

This template owns the shared governance files — the paths listed in `.github/Oidefile`. Derived repositories track it through two automated flows:

- **Governance files** — `.github/workflows/oide.yml` runs [`iwamot/oide`](https://github.com/iwamot/oide), which pulls every path listed in `.github/Oidefile` from this template and opens a PR. Its `SOURCES` input pins this template by tag, and Renovate tracks that pin, so tagging a new template release bumps it, which triggers the pull. `.github/Oidefile` lists itself, so adding a path to the template's manifest propagates to every derived repo in one pull.
- **Version pins** — Renovate keeps the action SHAs in `.github/workflows/*.yml` and the task ref in `mise.toml` current.

## Post-creation setup

After clicking **Use this template**:

1. **Replace this README.md** with the new repository's own description.
2. **Install the Renovate App** (or your self-hosted equivalent) for the new repo.
3. **Create a GitHub Environment named `production`** and add environment-scoped secrets:
   - `RENOVATE_APP_CLIENT_ID` and `RENOVATE_APP_PRIVATE_KEY`, for `renovate.yml` (another environment can be named with its `environment` input)
   - `OIDE_APP_CLIENT_ID` and `OIDE_APP_PRIVATE_KEY`, for `oide.yml` (always `production`)
4. **Add a release workflow** if the repo ships artifacts. These also take an `environment` input — create additional environments as needed:
   - `iwamot/workflows/.github/workflows/release-ghcr.yml` for GHCR
   - `iwamot/workflows/.github/workflows/release-homebrew-tap.yml` for Homebrew tap
5. **Add language-specific files** as needed: `Dockerfile`, `package.json`, `pyproject.toml`, `.gitignore`, etc.
6. **Extend `validate.sh`** with repo-specific lint (e.g. `mise run docker-lint Dockerfile`, language linters).
7. **Review `mise.toml`'s `min_version`**: the template provides a default, but the minimum mise version is each repository's own decision. Bump it if your tasks require a newer feature, or drop it if no constraint is needed. This is *not* auto-bumped by Renovate.
