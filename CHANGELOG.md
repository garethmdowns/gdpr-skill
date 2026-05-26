# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [1.3] - 2026-05-26

First public release. Applied three substantive corrections and five consistency fixes ahead of opening the skill to public contribution.

### Added

- `references/transfer-mechanisms.md`: how to record DPF status (reviewer to verify) separately from the contractual fallback (UK Addendum to EU SCCs or UK IDTA), when a Transfer Risk Assessment is required, and what the Confidence column measures under the new schema.
- `references/personal-data-vs-business-contact.md`: distinguishes personal data of natural persons (sole traders, named contacts at companies) from information about legal entities, with worked examples and the PECR Regulations 22 / 23 split between individual and corporate subscribers.
- Listings, directories, and supplier databases subsection in `references/lawful-basis.md`.
- Article 21 right-to-object cross-reference from `references/lawful-basis.md` to the privacy notice scaffolding section of `SKILL.md`.

### Changed

- Transfer register column schema: replaced the single "Likely mechanism" column with three columns (DPF status, contractual fallback, TRA required). Template asset and `SKILL.md` instructions updated together.
- Privacy notice scaffolding now instructs Claude to name the LLM provider explicitly and provides a template, aligning with ICO Generative AI guidance.
- Processor lookup entries that previously collapsed DPF and SCCs (Google Analytics, Meta Pixel, Google Ads, LinkedIn Insight, Mailchimp, PayPal) rewritten to record the contractual fallback as default with DPF status flagged for reviewer verification.
- Business function list reconciled between `SKILL.md` and `references/ico-template.md` (Service delivery, Marketing and growth).
- "UNKNOWN - reviewer to confirm" adopted as the canonical phrasing for unknown values throughout, replacing "manual review required".
- PECR coverage in `references/lawful-basis.md` now explicitly distinguishes Regulation 22 (individual subscribers) from Regulation 23 (corporate subscribers).
- Inline disclaimer block at the top of `SKILL.md` is now the canonical source for output framing.

### Removed

- `assets/output-header.md`: superseded by the inline disclaimer block in `SKILL.md`.

[Unreleased]: https://github.com/garethmdowns/gdpr-skill/compare/v1.3...HEAD
[1.3]: https://github.com/garethmdowns/gdpr-skill/releases/tag/v1.3
