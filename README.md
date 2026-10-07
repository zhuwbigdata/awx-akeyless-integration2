# awx-akeyless-integration2

AWX demo that pulls a Linux username/password from Akeyless through the HashiCorp
Vault Proxy (HVP) endpoint on an Akeyless Gateway into a Machine credential, then
prints them twice, 10 minutes apart, to show what the job receives (e.g. after a rotation).

## AWX wiring

| AWX object | Name | Notes |
|---|---|---|
| Credential (HashiCorp Vault Secret Lookup) | `akeyless_hvp_gateway_app_role` | URL `https://<gateway>/hvp/v1`, AppRole auth |
| Credential (Machine) | `linux_server_cred` | `username` / `password` linked to `akeyless_hvp_gateway_app_role`, path `kv/devops/ansible/test`, keys `username` / `password` |
| Project | `Akeyless HVP Credential Print` | SCM: this repo, branch `main` |
| Job template | `Print HVP Linux Credentials` | playbook `playbooks/print_hvp_credentials.yml`, inventory `Demo Inventory`, credential `linux_server_cred` |

AWX passes a Machine credential to ansible as the connection user and SSH password,
which the playbook reads as `ansible_user` / `ansible_password`.

Note: the playbook prints the password in clear text in the job output. It is meant for demos only.
