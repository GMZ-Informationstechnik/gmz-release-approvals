# Contract Release Approval Protocol v1

## Purpose

GitHub Team does not provide protected-environment reviewers for private
repositories. The coding credential and Robert's normal `irongeeks` identity
are also shared, so a workflow-dispatch click from that account is not an
independent human approval.

This protocol separates preparation from approval:

1. `irongeeks` or an agent creates a public approval-request issue containing
   only the proposed release metadata.
2. Robert signs in separately as `Bumblebob-GMZ` and posts the exact canonical
   approval comment.
3. The private Contracts workflow receives the comment ID and validates it
   directly through GitHub's API before signing or publishing anything.

## Canonical approval comment

The comment is one line of UTF-8 JSON without a Markdown code fence, leading or
trailing whitespace, or additional prose:

```text
{"action":"approve_contract_release","approval_version":1,"repository":"GMZ-Informationstechnik/gmz-platform-contracts","source_sha":"<40 lowercase hex characters>","tag":"contracts-python-v<package version>","version":"<package version>"}
```

All keys are required and no additional key is allowed.

## Required verifier checks

The private release workflow must reject unless all conditions hold:

1. the comment exists in this exact repository;
2. the immutable GitHub author ID is exactly `262149236`;
3. the author login currently resolves case-insensitively to
   `Bumblebob-GMZ`;
4. the comment body is canonical JSON and contains exactly the six fields
   above;
5. repository, source SHA, version and tag exactly equal the release inputs;
6. the comment is not older than 24 hours and is not dated in the future;
7. the target source SHA is still the head of private Contracts `main`;
8. repository-level immutable releases remain enabled;
9. the release tag and published release do not already exist;
10. the approval reference and body digest are included in the signed release
    manifest.

Editing a comment changes its body and `updated_at`; the workflow re-fetches
the live value. Deleting or hiding an approval makes verification fail. An old
approval cannot authorize a different source SHA, tag or version.

## Example workflow

The request issue should state the exact values and provide the ready-to-copy
canonical comment. Robert must independently compare those values with the
reviewed Contracts `main` commit before posting from `Bumblebob-GMZ`.

After a successful immutable release, the request issue may be closed. The
GitHub release, Sigstore bundle and release manifest remain the authoritative
release evidence.
