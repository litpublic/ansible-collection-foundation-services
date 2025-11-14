Ansible role to manage openshift operator from the operator hub
================================================================

This role install or uninstall operator from the operator hub. For manual approved subscriptions, the install plan can be approved by ansible.
It's also possible to install a specific operator Version by using ocp_manage_operator_starting_csv.


Role Variables
--------------

With the values in defaults/main.yml Red Hat Single Sign-On Operator will be installed.

Example Playbook
----------------

```yaml
---
- name: Install rhsso
  hosts: localhost
  vars:
    ocp_manage_operator_name: rhsso-operator
    ocp_manage_operator_namespace: rhsso
    ocp_manage_operator_channel: stable
    ocp_manage_operator_source: redhat-operators
    ocp_manage_operator_source_namespace: openshift-marketplace
    ocp_manage_operator_target_namespaces: true
    ocp_manage_operator_deployment_name: rhsso-operator
    ocp_manage_operator_create_namespace: true
  roles:
    - role: ocp_manage_operator
      tags:
        - rhsso
```

License
-------

This Ansible role is licensed under the BSD-3-Clause

Author
------

Dirk Egert <github@degert-it.de>
