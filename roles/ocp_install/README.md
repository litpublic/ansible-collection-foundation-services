# OCP Install Role

Automates an agent-based OpenShift Container Platform deployment: it prepares configuration artifacts, manages ignition files, powers on infrastructure, and persists generated secrets (e.g., kubeconfig) in Vault.

## Usage

```yaml
- hosts: localhost
  gather_facts: false
  roles:
    - role: lit.foundation_services.ocp_install
      vars:
        ins_cluster_name: demo
        ins_base_domain: corp.example.com
        ins_delegate_to: localhost
        ins_machine_network: 10.10.0.0/23
        ins_vsphere: true
        ins_vcenter_hostname: vcsa.corp.example.com
        ins_vcenter_username: administrator@vsphere.local
        ins_vcenter_password: "{{ lookup('env','VSPHERE_PASSWORD') }}"
        ins_pullsecret_lookup: secret=platform/data/demo/install:pullsecret
```

## Variables

- `ins_*`: core installation inputs covering cluster identity (`ins_cluster_name`, `ins_base_domain`, `ins_version`), topology/networking (`ins_machine_network`, `ins_gateway`, `ins_dns_servers`, `ins_ifname`), and image/pull secret data.
- vSphere-specific variables (`ins_vsphere`, `ins_vcenter_*`, `ins_vsphere_datacenter`, `ins_vsphere_network`, etc.) drive VM provisioning when targeting VMware.
- Vault integration relies on `ins_hashi_vault_auth_method`, `ins_engine_mount_point`, and lookup strings (`ins_pullsecret_lookup`, `g_root_ca_lookup`, etc.).
- Tags such as `ins_post` and `ins_infra` let you rerun post-install steps or infra/app-node configuration independently.
