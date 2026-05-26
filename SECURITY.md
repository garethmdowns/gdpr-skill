# Security Policy

## Framing

This skill is a Claude skill (instructions, reference files, and asset templates) that runs inside the user's Claude environment. It does not include a runtime service, does not exfiltrate code, and does not transmit anything to the maintainer.

The most likely "security" issues are:

- Information the skill instructs Claude to write into a draft output that should not be there (for example, a hardcoded secret reproduced verbatim in a draft ROPA cell instead of being flagged by location).
- Dependency vulnerabilities in the tooling Claude uses to generate outputs (notably openpyxl for the ROPA XLSX).
- Material errors in the reference files (transfer mechanisms, processor lookup) that would cause the skill to draft a misleading record.

## How to report a suspected vulnerability

Email [EMAIL TO BE FILLED] with a description, reproduction steps, and the skill version (release tag or commit hash) you observed it on. You can expect an acknowledgement within five working days.

Please do not open a public issue for suspected vulnerabilities until they have been triaged.

## Out of scope for security reports

False positives, false negatives, missed processors, and disagreements about UK GDPR interpretation are not security issues. They go in public issues using the appropriate template:

- False positive (skill flagged something it shouldn't have)
- False negative (skill missed something it should have caught)
- New processor suggestion

## Scope statement

The skill performs static analysis only. It reads files in the working directory through Claude's tool use, drafts outputs into a `gdpr-review/` directory, and does nothing else. It does not make outbound network calls of its own, does not phone home, and does not transmit findings outside the user's Claude conversation.

Every output is a draft. The skill does not produce attestations of compliance.
