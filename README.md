# litpublic.foundation_services (v1.0.0)

Roles included (prefix-free, modern):
- `vault` — HashiCorp Vault on RHEL (systemd or Podman/Quadlet) *(experimental stub)*
- `nexus` — Sonatype Nexus Repository on RHEL (systemd or Podman) *(experimental stub)*
- `gitlab_runner` — GitLab Runner on RHEL (systemd or Podman) *(experimental stub)*

> Collection-level scope: host services. Cluster-level config belongs to **GitOps**.

All current roles are still placeholders. They abort unless you explicitly opt in by setting
`lit_experimental_acknowledge=true`. Expect only debug output until the full implementations land.

Community note: contributors and users are expected to follow the [Ansible Community Code of Conduct](CODE_OF_CONDUCT.md).

## Syncing shared assets

If you need the shared scripts locally before CI runs, fetch them via:

```bash
curl -fsSL https://gitlab.com/lit4/modulix/platform/software-development-ecosystem/automation-tools/shared-assets/-/raw/main/collections/common/scripts/sync_shared_assets.sh | bash
```

## Shared assets

This repository keeps only the collection-specific code. All common files (CI config, lint/test scripts, Molecule scaffolding, etc.) are synchronised from the `shared-assets` project by CI. Run the command above if you need them locally ahead of the automated sync.

## CI/CD

- Pre-commit (`.pre-commit-config.yaml`) runs `ansible-lint` and `yamllint` locally. Install with `pip install pre-commit && pre-commit install`.
- `.gitlab-ci.yml` enforces `ansible-lint` and `molecule test --all` (docker driver) on every push/MR.
- `semantic_release` (default-branch pushes) runs `npx semantic-release`; via the official `@semantic-release/exec` plugin it patches `VERSION`, `galaxy.yml`, and the README to the new SemVer before tagging and committing (see `.releaserc.json`).
- `semantic_version_check` gates tags: `galaxy.yml` version must match the `vX.Y.Z` git tag before build/publish jobs run.
- The `VERSION` file must mirror the `galaxy.yml` version; CI checks both stay aligned.
- Tag releases as `vX.Y.Z` to build and publish to Ansible Galaxy automatically (requires protected `GALAXY_TOKEN`).
- Optional Automation Hub publishing runs when `REDHAT_PARTNER_TOKEN` is supplied.
- A `mirror_github` job pushes `main` and tags to the GitHub read-only mirror (`GITHUB_TOKEN`, `GITHUB_MIRROR_URL`).
- Local parity: run `./scripts/test-container.sh` to execute the same containerised lint/test flow as CI.

Required CI variables (protected + masked):

- `GALAXY_TOKEN` (https://galaxy.ansible.com/ui/token/)
- `GL_TOKEN` (or `GITLAB_TOKEN`) — Personal Access Token with `api` scope for semantic-release to create commits/tags/releases
- `GITHUB_TOKEN` and `GITHUB_MIRROR_URL` (PAT with `repo` scope, e.g. `https://oauth2:${GITHUB_TOKEN}@github.com/lightning-it/ansible-collection-foundation_services.git`)
- `REDHAT_PARTNER_TOKEN` *(optional, Automation Hub)*
- `SEMVER_GIT_USER_NAME` / `SEMVER_GIT_USER_EMAIL` *(optional override for release commits; defaults baked into runner image)*

Example release trigger:

```bash
git commit -am "feat: add vault role implementation"
git push origin main
```

When merged to `main`, semantic-release publishes the next `vX.Y.Z` tag automatically and the tag pipeline builds/publishes the collection.

