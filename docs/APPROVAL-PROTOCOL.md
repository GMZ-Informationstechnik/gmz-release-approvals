# Contract Release Approval Protocol v2

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
   directly through GitHub's REST and GraphQL APIs immediately before signing.
4. The workflow performs the complete live validation again immediately before
   publishing the immutable release.

## Canonical approval comment

The comment is one line of UTF-8 JSON without a Markdown code fence, leading or
trailing whitespace, or additional prose:

```text
{"action":"approve_contract_release","approval_version":2,"immutable_releases_enabled":true,"repository":"GMZ-Informationstechnik/gmz-platform-contracts","source_sha":"<40 lowercase hex characters>","tag":"contracts-python-v<package version>","version":"<package version>"}
```

All seven keys are required, `approval_version` must be the JSON number `2`,
`immutable_releases_enabled` must be the JSON literal `true`, and no additional
key is allowed. Key order, whitespace and escaping must match the canonical
one-line JSON shown above.

## Required verifier checks

The private release workflow must reject unless all conditions hold:

1. the comment exists on an open issue in this exact repository, the issue has
   the `contracts-release-approval` label and it is not a pull request;
2. the immutable GitHub author ID is exactly `262149236`;
3. the author login currently resolves case-insensitively to
   `Bumblebob-GMZ`;
4. the comment body is canonical JSON and contains exactly the seven fields
   above, including `approval_version:2` and
   `immutable_releases_enabled:true`;
5. repository, source SHA, version, tag and immutable-release assertion exactly
   equal the release inputs and prerequisites;
6. `created_at` equals `updated_at`, neither timestamp is in the future and
   `now - created_at` is no more than 24 hours;
7. GraphQL reports that the comment is not minimized and has never been edited;
8. the target source SHA is still the head of private Contracts `main`;
9. repository-level immutable releases remain enabled;
10. before the signing gate, no release tag, draft or published release exists;
    the workflow never adopts, deletes or rewrites pre-existing release state;
11. the signed release manifest binds the ledger repository, immutable numeric
    issue ID, issue number and URL, comment node ID, numeric ID and URL, author
    ID and observed canonical login, `created_at`, `updated_at`, canonical body
    digest, source SHA, version, tag, Main-CI run and artifact provenance, and
    the signing-gate verification timestamp;
12. at the first gate, the exact local package bytes still match the bound
    successful Contracts `main` CI artifact; only after the signed release
    manifest and `SHA256SUMS` exist may the workflow create its own draft and
    upload the base release assets;
13. immediately before publication, the only existing release state is the
    exact draft created by this workflow run; the complete approval, issue,
    comment, Contracts `main`, CI-provenance and immutable-release checks all
    run again;
14. before creating the publication attestation, every remote base-asset name,
    size and SHA-256 digest exactly matches both the signed Gate-1 release
    manifest and `SHA256SUMS`;
15. a separate signed publication attestation binds `publishVerifiedAt`, the
    ledger repository, immutable numeric issue ID, issue number and URL,
    comment node ID, numeric ID and URL, author ID and observed login,
    `created_at`, `updated_at`, canonical body digest, source SHA, version, tag,
    draft ID, `SHA256SUMS` digest, and every base asset name, size and SHA-256
    digest;
16. the publication-attestation payload and its Sigstore bundle are excluded
    from their own bound base-asset set. After uploading exactly those two
    evidence files, a final read-only check proves that all base assets remain
    unchanged, both remote evidence files match the exact locally signed bytes
    by size and SHA-256 digest, and no other asset was added before immutable
    publication.

The checks run as two distinct, non-circular gates. The first gate validates
the Main-CI bytes, produces the signed release manifest and checksums, then
creates a new draft. The second gate repeats the complete live validation,
accepts only that run's exact draft, and produces the separately signed
publication attestation before a final read-only asset check and immutable
publication. Any pre-existing draft, tag or release fails closed and requires
manual investigation; the workflow never mutates it.
Editing, deleting, minimizing or hiding an approval makes either gate fail. An
old approval cannot authorize a different source SHA, tag or version.

## Example workflow

The request issue should state the exact values and provide the ready-to-copy
canonical comment. Robert must independently compare those values with the
reviewed Contracts `main` commit before posting from `Bumblebob-GMZ`.

After a successful immutable release, the request issue may be closed. The
GitHub immutable release, `SHA256SUMS`, signed release manifest, both Sigstore
bundles and signed publication attestation remain the authoritative release
evidence.
