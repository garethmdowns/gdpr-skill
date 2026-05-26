# UK transfer mechanisms - DPF and the contractual fallback

This file explains how to populate the DPF status, contractual fallback, and TRA columns in the transfer register, and what the Confidence column measures under that schema. The skill must always treat DPF status and the contractual fallback as separate questions, not as alternatives in a single column.

## Why DPF and SCCs are not interchangeable

The UK-US Data Bridge (the UK extension to the EU-US Data Privacy Framework, commonly called "DPF") is a US-side self-certification regime. A US recipient is only covered by DPF if that specific recipient is on the active DPF list maintained by the US Department of Commerce, and the recipient's certification is current.

The UK Addendum to EU SCCs and the UK International Data Transfer Agreement (IDTA) are contractual safeguards. They live in the contract between the parties. They do not depend on the recipient's regulatory status with any third country authority.

These two routes do different jobs. DPF is a status that can be lost or withdrawn. SCCs and the IDTA are a contractual position that, once in place, continues to apply for the life of the contract.

## What the skill records

For each transfer to a non-adequate destination (the common case is a US recipient), the skill records three things:

1. **DPF status (reviewer to verify).** Default value: `Reviewer to verify against the current DPF list`. The skill never asserts that a recipient is or is not DPF-certified, because the list changes and certifications can be added or withdrawn between the time of scanning and the time the record is relied on. The reviewer checks the live list at https://www.dataprivacyframework.gov/list before the controller relies on DPF.

2. **Contractual fallback if DPF not relied upon.** Default value: `UK Addendum to EU SCCs`. The UK IDTA is an alternative that some controllers prefer for new contracts. The skill suggests the UK Addendum to EU SCCs as the default because it is commonly the default position in vendor DPAs. If the codebase shows evidence of an IDTA being in place (rare, would usually live outside the code), the skill notes this in the Notes column.

3. **TRA required if contractual fallback used.** Default value: `Yes` where the contractual fallback applies. The ICO expects a Transfer Risk Assessment whenever a controller relies on SCCs or the IDTA. The skill records the requirement, it does not perform the assessment.

The contractual fallback is the default position the skill recommends. DPF is the reviewer-verified alternative that can remove the need for SCCs only if the recipient's status is confirmed and the controller decides to rely on DPF.

## Non-US transfers

For transfers to a country with UK adequacy regulations (see the adequacy list in `processors.md`), the DPF and contractual fallback columns are not used. The transfer register row should show:

- DPF status: `Not applicable - adequacy regulations apply`
- Contractual fallback: `Not applicable - adequacy regulations apply`
- TRA required: `No`

For transfers to other third countries without adequacy (most non-EEA, non-US destinations), DPF does not apply. The row should show:

- DPF status: `Not applicable - DPF covers US recipients only`
- Contractual fallback: `UK Addendum to EU SCCs` (or `UK IDTA` if evidence supports it)
- TRA required: `Yes`

## Why the skill recommends the contractual fallback as default

Three reasons:

- DPF status is not visible in code. The skill cannot check the list at scan time.
- DPF status is fluid. A recipient certified today may be withdrawn tomorrow. The controller needs a fallback in place either way.
- Vendor DPAs commonly include the UK Addendum to EU SCCs as a default term. The skill recommends the contractual fallback because it is the position the controller can rely on regardless of DPF list status, with the reviewer to confirm the specific terms of each vendor's contract.

If the reviewer confirms the recipient is on the current DPF list and the controller is comfortable relying on DPF, the contractual fallback can be removed from the live record. The skill does not make that call.

## The Transfer Risk Assessment

Where the controller relies on SCCs or the IDTA, the ICO expects a TRA. The TRA evaluates whether the laws of the destination country undermine the protections in the contractual safeguard, with reference to the UK GDPR essential equivalent standard.

The TRA is a controller exercise. The skill flags the requirement but cannot perform it. Inputs the reviewer needs include the destination country's surveillance and access laws, the categories of data transferred, the likelihood of access requests, and any supplementary measures (encryption with controller-held keys, pseudonymisation, contractual reinforcement) that mitigate the risk.

## What the Confidence column measures

Under this schema, the mechanism columns carry built-in defaults. They are not the inferred element of the row. Confidence in the transfer register therefore measures the overall row classification, not just the country guess. Three questions sit behind it:

- Does the data actually go to this country, or is the residency assumption based on a default that the codebase cannot confirm?
- Is this genuinely a transfer for UK GDPR purposes, or is the data flow internal (UK-to-UK with a US-headquartered vendor that processes only in the UK region)?
- Is the relationship genuinely a processor relationship, or could it be a joint controller relationship (Meta Pixel, Google Ads, LinkedIn Insight Tag and similar are common joint controller patterns under CJEU Fashion ID)?

Confidence levels follow the same HIGH / MEDIUM / LOW convention as the rest of the skill:

- **HIGH**: code makes the residency, transfer status, and processor role explicit and unambiguous (e.g. AWS region pinned to `us-east-1` in config and personal data clearly written to that region; no joint controller ambiguity).
- **MEDIUM**: strong contextual signal but at least one of the three questions has room for doubt (e.g. Sentry is detected, default residency is US, but the project may have configured the EU region without it being visible in code).
- **LOW**: best guess on residency, transfer status, or role; reviewer must verify before relying on the row.

## What the skill does NOT do

- Does not check the live DPF list.
- Does not confirm SCCs or the IDTA are actually in place in the recipient's contract.
- Does not perform a Transfer Risk Assessment.
- Does not advise between UK Addendum to EU SCCs and the UK IDTA where both are viable. That is a controller decision based on contracting preference.
- Does not address transfers under Article 49 derogations. Where a derogation is suspected (one-off transfer, vital interests, explicit consent), the reviewer should be flagged separately.
