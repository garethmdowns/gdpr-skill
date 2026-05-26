---
name: gdpr
description: Scan a codebase to produce draft GDPR/UK GDPR artefacts. Use whenever the user wants to audit a repository for personal data processing, generate a draft ROPA (Record of Processing Activities), identify third party data flows, flag international transfers, detect AI/LLM data processing, or build a starting point for a privacy notice. Trigger on phrases like "gdpr scan", "privacy audit", "ropa", "record of processing", "data inventory", "what personal data does this app collect", "privacy review", "data protection review", "find personal data in this code", or any time the user references compliance, privacy notices, processors, or transfer registers in the context of a codebase. Always frame outputs as drafts requiring human review by a qualified person, never as compliance determinations.
---

# GDPR Codebase Review Skill

A skill for producing draft GDPR/UK GDPR artefacts from a codebase. The outputs are starting points for a qualified person (DPO, privacy consultant, data protection lead) to review, not compliance determinations.

## Core principle

This skill produces drafts. It cannot determine lawful basis with certainty, cannot confirm whether a transfer mechanism is in place, cannot validate retention against business need, and cannot see contracts, DPAs, or the operational reality behind the code. Every output must be framed as a draft for human review, with confidence indicators on inferences.

Err toward over-flagging. A false positive wastes a few minutes of review. A false negative means a data flow goes unrecorded.

## When to invoke

The user has asked for one or more of:
- A GDPR/privacy audit or scan of a codebase
- A draft ROPA, processor register, or transfer register
- A list of personal data processed by an application
- An audit of third party data flows or international transfers
- AI/LLM specific privacy detection
- Input for a privacy notice

If the user mentions a specific subdirectory (e.g. "scan /api" or "just the backend"), treat that as the scope. Otherwise default to the whole repository, excluding standard directories (node_modules, .git, vendor, dist, build, .next, target, venv, __pycache__).

## What the skill produces

Five outputs by default, written to a `gdpr-review/` directory at the repo root (or wherever the user specifies):

1. `gdpr-review/summary.md` - human-readable markdown report covering all findings
2. `gdpr-review/ropa-draft.xlsx` - structured draft ROPA aligned to the ICO controller template (green-shaded mandatory Article 30(1) columns, blue-shaded ICO-recommended useful additional columns). One row per identified processing activity, grouped by business function. Built from `assets/ropa-template.xlsx`.
3. `gdpr-review/transfer-register.csv` - draft international transfer register
4. `gdpr-review/ai-processing.md` - dedicated section for AI/LLM data processing findings
5. `gdpr-review/methodology.md` - what was scanned, what was excluded, what confidence the findings have, what was NOT checked

The methodology file is critical. It tells the reviewer where to focus their attention and protects against the output being treated as definitive.

The ROPA is always XLSX. The green/blue colour coding mirrors the ICO original and carries practitioner meaning (green = legally mandatory, blue = ICO-recommended), which is lost in CSV. Do not produce a CSV version of the ROPA.

## Scan dimensions

The skill scans across the following dimensions. The first four are the substantive findings the skill produces; dimensions 5-8 are cross-cutting overlays that affect lawful basis and rights analysis throughout.

### 1. Personal data categories

Identify personal data the application processes. Look for:

- Database schemas, migrations, ORM models (column names, table names, type hints)
- Form fields and validation schemas (HTML forms, React/Vue components, Zod/Joi/Yup schemas, Pydantic models)
- API request and response shapes (route handlers, OpenAPI/Swagger definitions, GraphQL schemas)
- Logging statements (what gets written to logs)
- Cookies and local storage keys
- Environment variables referencing user data fields

Map detected fields to categories. Use the reference table in `references/data-categories.md` as the canonical taxonomy. Categories include identifiers (name, email, phone), location data, financial data, health data, biometric data, behavioural data, device data, communications content, special category data (Article 9), and criminal offence data (Article 10).

For each category found, capture:
- Category name
- Specific fields detected (e.g. "email" field in `users` table, `req.body.email` in `/api/signup`)
- Source location(s) in the code
- Inferred purpose (with confidence indicator: HIGH/MEDIUM/LOW)
- Likely lawful basis suggestion (with confidence indicator, always flagged as suggestion only)

### 2. Third party data flows

Identify every external service that personal data flows to. Scan:

- Package manifests (package.json, requirements.txt, Gemfile, go.mod, pom.xml, etc.)
- Imports and SDK initialisation calls
- HTTP client calls to external domains
- Environment variables and config files for API keys, endpoints, service URLs
- Embedded scripts and pixels in HTML/JSX (analytics, ads, chat widgets, tag managers)

Use the lookup table in `references/processors.md` to identify known processors, their typical role (controller/processor/joint controller), their data residency, and applicable transfer mechanisms. The table covers common ones: Stripe, Segment, Mixpanel, Amplitude, Sentry, PostHog, Google Analytics, Meta Pixel, Hotjar, Intercom, HubSpot, Mailchimp, SendGrid, Twilio, AWS, GCP, Azure, OpenAI, Anthropic, Cohere, Pinecone, Supabase, Auth0, Clerk, and similar.

If a service is found that is not in the lookup table, flag it as "UNKNOWN - reviewer to confirm" and note what data appears to be sent to it.

### 3. International transfers

For each third party identified, determine likely transfer status:
- UK/EEA data residency (no transfer)
- US transfer (most common, record DPF status separately from the contractual fallback)
- Other third country transfer
- Unknown residency (flag for review)

Read `references/transfer-mechanisms.md` before populating the transfer register. The file explains why DPF status and the contractual fallback are recorded separately, how to set defaults that protect against a stale DPF list, when a Transfer Risk Assessment is required, and what the Confidence column measures under this schema.

Output to `transfer-register.csv` with columns: Service, Purpose, Data categories transferred, Destination country/region, DPF status (reviewer to verify), Contractual fallback if DPF not relied upon, TRA required if contractual fallback used, Confidence, Notes, Source files.

Always note that this is based on the default data residency of the provider. Many providers offer EU-only options that the codebase cannot see. Flag this in the methodology file.

### 4. AI and LLM specific processing

This is a distinct dimension because the regulatory landscape is moving fast and the risks differ from typical SaaS. Scan for:

- LLM API calls (OpenAI, Anthropic, Cohere, Mistral, Bedrock, Vertex AI, Azure OpenAI, etc.)
- Vector databases and embedding stores (Pinecone, Weaviate, Qdrant, Chroma, pgvector)
- ML inference endpoints (Hugging Face Inference API, Replicate, Modal, RunPod)
- Local LLM deployments (Ollama, llama.cpp, vLLM)
- Speech/vision APIs (Whisper, AssemblyAI, Deepgram, Google Vision, Azure Cognitive Services)

For each, flag:
- What data appears to be sent to the model
- Whether prompts include personal data (flag: yes/likely/unclear)
- Whether outputs are logged or stored
- Whether any code suggests training-on-user-data (fine-tuning endpoints, data export to training pipelines)
- Whether the system makes automated decisions that significantly affect users (Article 22 territory)

Output to `ai-processing.md` with these findings, plus a "DPIA likely required if..." section calling out the patterns that would typically trigger Article 35.

### 5. Cross-cutting flags

Issues that don't fit the four dimensions above but matter:

- **PII in logs**: log statements that appear to write personal data (email, name, full request body, etc.)
- **PII in URLs**: route parameters or query strings that carry personal data (`/user/email@example.com`, `?email=...`)
- **Secrets in code**: hardcoded API keys, tokens, connection strings (flag location, do NOT reproduce the secret in output)
- **Missing retention signals**: tables/collections with personal data and no apparent retention logic (no TTL, no archival cron, no delete endpoints)
- **Special category data signals**: fields suggesting health, religious belief, ethnicity, sexual orientation, political opinion, trade union membership, biometrics, genetic data. Also free-text fields and image uploads where these could be ingested incidentally.
- **DSR readiness**: presence or absence of endpoints/flows for access, deletion, rectification, portability. Note where deletion appears hard (heavily denormalised data, foreign key chains, immutable logs). Note whether sub-processor data is included in access/deletion flows.

### 6. The PECR overlay

PECR (Privacy and Electronic Communications Regulations 2003) sits over UK GDPR and frequently requires consent even where a UK GDPR lawful basis would otherwise support the processing. Read `references/lawful-basis.md` for the full PECR overlay guidance. The skill must flag:

- **PECR Regulation 6** applicability: any product analytics SDK, advertising/attribution SDK, session replay tool, or A/B testing tool that stores or accesses information on the user's device for purposes that are not strictly necessary to deliver the service the user has requested. Consent is required for the SDK to operate at all, regardless of the UK GDPR basis suggested.
- **PECR Regulation 22** applicability: any push notification, email, SMS, or in-app message logic that appears non-transactional (engagement nudges, feature promotion, premium upsell, re-engagement campaigns). Consent is required unless the B2C soft opt-in exception applies (existing customer, similar product, opt-out offered).

In the ROPA, where PECR applies, the lawful basis cell should suggest the UK GDPR basis AND flag the PECR consent requirement. Where PECR consent is required and the codebase shows no consent management, flag this as a priority recommendation.

### 7. AADC (Age Appropriate Design Code)

The ICO's statutory Age Appropriate Design Code applies to online services "likely to be accessed by children" in the UK, regardless of intended audience. The skill should flag AADC consideration whenever:

- The app or service is publicly available without age verification on first launch
- The codebase contains no age-gate or age-affirmation flow
- The product surface (free tier, gamification, social features, AI companion features) could plausibly attract under-18 users
- Marketing copy or store positioning suggests teen relevance

This is independent of Article 6 lawful basis and independent of Article 8 (parental consent under Consent basis). The skill should surface AADC in the methodology output and in the summary's recommendations, not bury it inside the children's-data flag.

### 8. Article 21 right-to-object surfacing

Where any processing activity is suggested with Legitimate interests as its lawful basis, the skill must surface the Article 21 right-to-object obligation in the ROPA row (in the "Information rights process" column) and in the privacy notice starting points section of the summary.

## How to scan

Read `references/scanning-strategy.md` for the recommended approach. The high-level flow:

1. Take a directory tree first to understand the repo shape
2. Read manifests and config to identify the stack
3. Pick scanning targets based on stack (e.g. for a Next.js + Prisma app: schema.prisma, /pages/api, /app/api, components with forms)
4. Read those targets, building findings as you go
5. Cross-reference findings against the lookup tables in `references/`
6. Generate the five output files
7. Generate the methodology file LAST, after all scanning is complete, listing what was and was not covered

Do not try to read every file in a large repo. Be strategic: schema/migration files, API route handlers, form components, and config files give you 80% of the picture.

## Output framing

Every output file must contain, at the top:

```
DRAFT - FOR REVIEW BY A QUALIFIED PERSON

This document was generated by an automated scan of the codebase. It is a starting
point for human review, not a compliance determination. Findings may be incomplete
or incorrect. Lawful basis, transfer mechanisms, retention periods, and the
operational reality behind the code cannot be determined from code alone.

Generated: [ISO date]
Repository scanned: [path]
Scope: [whole repo / specific dirs]
```

Use UK English in all output (organisation not organization, behaviour not behavior, etc.).

Confidence indicators on inferred fields use three levels: HIGH (the code makes it explicit), MEDIUM (strong contextual signal), LOW (best guess, flag for review).

## ROPA draft structure

The ROPA output is `gdpr-review/ropa-draft.xlsx` and mirrors the ICO's "Documentation for Controllers" template. Read `references/ico-template.md` for the full column structure, the mandatory vs useful-additional distinction, and worked examples before populating it.

Always start from `assets/ropa-template.xlsx`. It has the green/blue colour coding, the section headers (Mandatory under Article 30(1) / Useful additional information), the instructions sheet, the frozen panes, and the correct column widths. Open it with openpyxl, populate the rows, and save to `gdpr-review/ropa-draft.xlsx`.

The 16 green-shaded columns are required (Article 30(1) UK GDPR). The 9 blue-shaded columns are ICO-recommended but not legally required. Populate both. Where a value is genuinely unknown from code alone, write `UNKNOWN - reviewer to confirm` rather than leaving blank or guessing.

Do not produce a CSV version of the ROPA. The green/blue colour coding carries practitioner meaning (legally mandatory vs ICO-recommended) that does not survive CSV. The XLSX is the only ROPA output format.

Group rows by **business function**, then by **purpose**. Do not produce one row per API endpoint or per database table. A small consumer app typically results in 6-12 rows.

Common business functions for a SaaS or consumer app:
- Account management
- Service delivery (the core product feature)
- Payments and subscription
- Customer support
- Marketing and growth
- Product analytics
- Security and fraud prevention

## Privacy notice scaffolding

In `summary.md`, include a "Privacy notice starting points" section that lists, in plain English:

- What categories of data the application appears to collect
- What it appears to be used for
- Who it appears to be shared with
- Where it appears to go (countries)
- AI/automated processing (if any)

For each AI/LLM service detected, name the provider explicitly. The ICO's guidance on generative AI pushes controllers toward naming third party AI services in privacy notices rather than describing them as a generic "AI service". Use the following template for the AI processing bullet, filling in each placeholder from the findings in `ai-processing.md` and the transfer register:

`We use [PROVIDER NAME] (a [REGION]-based AI service provider) to [DESCRIBE PURPOSE]. When we use this service, we send: [DATA CATEGORIES]. We do not send any data you submit to us [verify against actual data flow; amend if data subjects' submitted content is also sent]. [PROVIDER NAME] does not use the data we send to train its models [confirm against current provider terms]. The data is transferred to [COUNTRY]. We rely on [TRANSFER MECHANISM - confirmed by reviewer] for this transfer.`

The bracketed cues are instructions to the drafter, not text to leave in the published privacy notice. Where the skill cannot determine the answer from code alone, write `UNKNOWN - reviewer to confirm` in the relevant placeholder rather than guessing.

This is NOT a privacy notice. It is raw material a drafter can use as a starting point, framed accordingly.

## What this skill explicitly does NOT do

State these in `methodology.md`:

- Determines lawful basis (suggestions only)
- Confirms whether processor contracts/DPAs are in place
- Validates retention periods against business need
- Audits security controls beyond what is visible in code
- Reviews physical security, organisational measures, training, policies
- Checks whether DPIAs have been done
- Verifies marketing consent records
- Audits cookie banner behaviour at runtime (only what is in code)
- Replaces a qualified person performing a proper data protection assessment

## Limitations to be honest about

- Purpose inference is the weakest dimension. Where naming is unclear, the skill should mark LOW confidence rather than guessing.
- Static analysis only. Runtime behaviour (what data is actually sent, what cookies are actually set on production) is not visible.
- Comments and naming may be misleading or stale.
- Third party SDK behaviour evolves. The lookup table is a snapshot.
- The skill sees code, not contracts. A processor relationship in code may be a joint controller relationship in legal reality, or vice versa.

## File map

- `references/ico-template.md` - the ICO ROPA column structure with mandatory vs useful-additional distinction. **Read this before populating any ROPA output.**
- `references/data-categories.md` - canonical taxonomy of personal data categories with detection patterns
- `references/processors.md` - lookup table of common third party services with role, residency, and transfer notes
- `references/scanning-strategy.md` - stack-specific scanning advice (Next.js, Django, Rails, etc.)
- `references/lawful-basis.md` - how to suggest a lawful basis with confidence indicators; covers PECR overlay and AADC
- `references/transfer-mechanisms.md` - how to record DPF status separately from the contractual fallback (UK Addendum to EU SCCs or UK IDTA), when a TRA is required, and what the Confidence column measures. **Read this before populating the transfer register.**
- `references/personal-data-vs-business-contact.md` - the distinction between personal data of natural persons and information about legal entities; how to split the analysis where both are present
- `assets/ropa-template.xlsx` - empty ICO-aligned ROPA template (the only ROPA output format; green/blue colour-coded)
- `assets/transfer-register-template.csv` - empty transfer register template

Read references on demand. Do not load all of them upfront.
