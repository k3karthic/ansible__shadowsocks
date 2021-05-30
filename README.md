# Ansible - Shadowsocks Proxy

The Ansible playbook in this repository configures a FreeBSD 13 instance to act as a SOCKS proxy for [NetGuard](https://www.netguard.me/) using [Shadowsocks](https://shadowsocks.org/en/index.html).

The playbook assumes the instance runs in Google Cloud using the scripts below,
- [https://github.com/k3karthic/terraform__gcloud-instance](https://github.com/k3karthic/terraform__gcloud-instance)
- [https://github.com/k3karthic/ansible__freebsd-basic](https://github.com/k3karthic/ansible__freebsd-basic)

## Requirements

Install the following Ansible modules before running the playbook,
```
ansible-galaxy collection install community.general

pip install google-auth requests
ansible-galaxy collection install google.cloud
```

## Dynamic Inventory

This playbook uses the Google [Ansible Inventory Plugin](https://docs.ansible.com/ansible/latest/collections/google/cloud/gcp_compute_inventory.html) to populate public FreeBSD instances dynamically.

Public instances are assumed to have a label `shadowsocks_service: yes`.

## Playbook Configuration

1. Create `inventory/google.gcp_compute.yml` based on `inventory/google.gcp_compute.yml.sample`,
    1. specify the project id
    1. specify the zone where you have deployed your server on Google Cloud
    1. Configure the authentication
1. Set username and ssh authentication in `inventory/group_vars/all.yml`.
1. Create the server configuration `files/config.json` using `files/config.json.sample`.

## Android Configuration

**Step 1.** Install [Shadowsocks FOSS](https://www.f-droid.org/en/packages/com.gitlab.mahc9kez.shadowsocks.foss/) and allow it to bypass [NetGuard](https://f-droid.org/en/packages/eu.faircode.netguard/) by de-selecting the "Apply rules and conditions" checkbox.

<img src="https://github.com/k3karthic/ansible__shadowsocks/raw/main/resources/shadowsocks_screenshot.jpg" width="500" />

**Step 2.** Save the server configuration as a profile in Shadowsocks FOSS.

<img src="https://github.com/k3karthic/ansible__shadowsocks/raw/main/resources/netguard_screenshot.jpg" width="500" />

## Deployment

Run the playbook using the following command,
```
./bin/apply.sh
```

## Encryption

Sensitive files like the Shadowsocks config and SSH private keys are encrypted before being stored in the repository.

You must add the unencrypted file paths to `.gitignore`.

Use the following command to decrypt the files after cloning the repository,

```
./bin/decrypt.sh
```

Use the following command after running terraform to update the encrypted files,

```
./bin/encrypt.sh <gpg key id>
```
