# awx-akeyless-integration2

AWX demo that pulls a Linux service-account username/password from Akeyless through
the HashiCorp Vault Proxy (HVP) endpoint on an Akeyless Gateway, injects them into a
job as extra vars, and prints them twice, 10 minutes apart, to show what the job
receives (e.g. after a rotation).

## Layout

- `playbooks/print_hvp_credentials.yml`: prints `Username_Lin_svc` / `Password_Lin_svc`, pauses 600s, prints again.
- `extensions/awx/credential_types/linux_service_account.yml`: custom credential type that injects those two extra vars.

## AWX wiring

| AWX object | Name | Notes |
|---|---|---|
| Credential (HashiCorp Vault Secret Lookup) | `akeyless_hvp_gateway_app_role` | URL `https://<gateway>/hvp/v1`, AppRole auth |
| Credential type | `Linux Service Account (Akeyless HVP)` | from `extensions/awx/credential_types/linux_service_account.yml` |
| Credential | `linux_svc_hvp_vars` | `username` / `password` linked to `akeyless_hvp_gateway_app_role`, path `kv/devops/ansible/test`, keys `username` / `password` |
| Project | `Akeyless HVP Credential Print` | SCM: this repo, branch `main` |
| Job template | `Print HVP Linux Credentials` | playbook `playbooks/print_hvp_credentials.yml`, inventory `Demo Inventory`, credential `linux_svc_hvp_vars` |

A Machine credential (e.g. `linux_server_cred`) does **not** work for this playbook:
AWX passes Machine credentials as SSH connection settings, not as playbook variables,
so `Username_Lin_svc` / `Password_Lin_svc` would be undefined.

Note: the playbook prints the password in clear text in the job output. It is meant for demos only.
