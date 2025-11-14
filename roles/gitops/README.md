# GitOps Role

Installs and manages the OpenShift GitOps (Argo CD) operator, seeds the initial App-of-Apps tree, and wires Vault-backed repository secrets.

## Usage

```yaml
- hosts: localhost
  gather_facts: false
  roles:
    - role: litpublic.foundation_services.gitops
      vars:
        gitops_operator_channel: gitops-1.18
        gitops_operator_state: present
        git_repo_templates:
          platform-repo:
            namespace: openshift-gitops
            repo: https://git.example.com/platform.git
            username: git
            password: changeme
```

## Variables

- `gitops_operator_*`: tune the Subscription (channel, source, starting CSV, approval, extras).
- `gitops_operator_state` / `gitops_argocd_state`: control whether the operator/App-of-Apps exist (default `present`).
- `git_repo_templates`: dictionary describing Git credentials to render into secrets; supports `vault_lookup` entries when `git_vault_enabled: true`.
- `git_vault_enabled`, `git_external_secrets_path`, `g_vault_*`: enable HashiCorp Vault lookups for repo passwords and external secret approles.
