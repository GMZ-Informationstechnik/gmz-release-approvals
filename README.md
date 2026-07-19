# GMZ Release Approvals

This public repository is a deliberately non-executable human approval ledger
for selected GMZ release metadata.

It contains no product source, customer data, credentials, secrets, deployment
plans or infrastructure access. Creating an issue is only a request and never
an approval. A release is approved only by an exact canonical comment authored
by the separately controlled GitHub account `Bumblebob-GMZ` (numeric user ID
`262149236`).

The approval comment binds:

- the private target repository name;
- the exact immutable source commit SHA;
- the exact package version and release tag;
- the single action `approve_contract_release`;
- approval protocol version `1`.

The private release workflow re-fetches the public comment immediately before
signing and publishing. It checks the immutable numeric author ID, canonical
body, freshness and all bound release values. A coding agent may prepare the
request, but cannot author the trusted approval comment.

See [the approval protocol](docs/APPROVAL-PROTOCOL.md).

## Public information

Only repository names, release versions, commit hashes, tags, timestamps and
approval records belong here. Treat every other class of information as
prohibited.

## Non-capabilities

This repository has no release workflow, deployment workflow, credentials,
secret resolver, package publisher or infrastructure integration. Its contents
do not authorize a deployment.
