# Agent Instructions

## Repository role

This public repository is a non-executable human approval ledger. It is not a
source repository, package repository or deployment controller.

## Mandatory boundaries

- Never add credentials, secrets, customer data, private source, internal
  network details, plans, state or runtime evidence.
- Never add release, deployment, shell, SSH, Proxmox, DNS, UniFi, PBS, Zabbix
  or secret-resolution automation.
- Agents may prepare approval-request issues, but must never claim to be the
  human approver or create an approval comment.
- Only an exact comment authored by GitHub user ID `262149236` is a candidate
  human approval. The private target workflow performs the final verification.
- An approval authorizes only the exact package release named in the comment.
  It never authorizes infrastructure execution.
