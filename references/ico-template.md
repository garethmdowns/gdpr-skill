# ICO ROPA template structure

This is the canonical structure for the ROPA output. It mirrors the ICO's official "Documentation for Controllers" template (`gdpr-documentation-controller-template.xlsx`), available at https://ico.org.uk/for-organisations/uk-gdpr-guidance-and-resources/accountability-and-governance/documentation/.

The ICO template has two sections:

- **Mandatory fields** (green-shaded in the ICO original): the information UK GDPR Article 30(1) requires controllers to document.
- **Useful additional fields** (blue-shaded in the ICO original): information the ICO recommends maintaining alongside the ROPA but that Article 30 does not require.

The skill's ROPA output preserves this distinction. When the user asks for an "ICO-style ROPA" or just a "ROPA", use this structure as the default.

## Approach: broad to narrow

The ICO recommends a "broad to narrow" approach. Each ROPA entry should start with a **business function** (HR, Sales, Customer Services, Product, Marketing, etc.), within which there are **purposes**, within which there are **categories of data subjects**, and within which there are **categories of personal data**.

For a typical consumer app codebase, business functions might include:
- Account management
- Product delivery (the core feature, e.g. AI-driven recommendations, content generation, search)
- Payments and subscription
- Customer support
- Marketing
- Product analytics
- Security and fraud prevention

Each business function becomes one or more rows in the ROPA. Do not produce a row per API endpoint or per database table. Group by purpose.

## Mandatory fields (Article 30(1))

These columns are required. Always populate them, even if the value is "UNKNOWN - manual review required".

1. **Controller name** - the organisation that determines the purposes and means of the processing.
2. **Controller contact details** - usually email, postal address, or website.
3. **Controller representative name and contact** (if applicable) - where the controller is outside the UK but offers services to UK individuals.
4. **DPO name and contact** (if applicable) - if a DPO has been designated.
5. **Joint controllers** (if applicable) - other organisations that jointly determine purposes and means.
6. **Purpose of processing** - why personal data is used (e.g. "to provide the core service users have signed up for"). One row per purpose.
7. **Lawful basis for processing** - the Article 6 basis. The skill suggests with confidence indicator; the reviewer determines.
8. **Special category condition** (if applicable) - the Article 9 condition where special category data is processed.
9. **Criminal offence data condition** (if applicable) - the Article 10 condition.
10. **Categories of individuals (data subjects)** - the types of people whose data is processed (e.g. customers, prospects, employees, users of the free tier, users of the paid tier).
11. **Categories of personal data** - the types of data (e.g. contact details, account credentials, payment information, user-generated content, behavioural data).
12. **Categories of recipients** - everyone the data is shared with, internal and external (e.g. payment processor, email service provider, cloud hosting provider, AI model provider).
13. **Names of third countries or international organisations** (if applicable) - where data is transferred outside the UK.
14. **Safeguards for international transfers** - the mechanism in place (UK adequacy regulations, UK IDTA, UK Addendum to EU SCCs, DPF, BCRs, Article 49 derogation). The skill notes what is LIKELY NEEDED; the reviewer confirms what is in place.
15. **Retention schedule** - how long data is kept. The skill marks UNKNOWN unless code makes retention explicit.
16. **General description of technical and organisational security measures** - a brief description. Only populate from what is visible in code (e.g. "TLS in transit; encryption at rest visible in Supabase configuration"); otherwise UNKNOWN.

## Useful additional fields (ICO recommends, not Article 30 required)

These columns are not legally required but the ICO suggests maintaining them. They add real value for accountability.

17. **Business function or area** - the part of the business owning this processing (e.g. Product, Engineering, Marketing). Used for the broad-to-narrow grouping.
18. **Source of personal data** - whether collected directly from the individual or from a third party. For most app codebases, this is "directly from the individual".
19. **Where personal data is stored** - the system(s) where the data resides (e.g. "Supabase Postgres database; user-uploaded images in S3 bucket"). Useful for DSR fulfilment.
20. **Data sharing agreements in place** - links to DPAs, joint controller agreements, etc. The skill marks UNKNOWN - code cannot tell us this.
21. **Privacy notice link** - where the data subjects are informed about this processing.
22. **DPIA reference** - if a DPIA was done, where the record lives. The skill flags WHEN a DPIA is likely needed.
23. **LIA reference** - if legitimate interests is the basis, where the assessment lives.
24. **Information rights process** - how access, rectification, deletion, portability, objection are handled for this processing.
25. **Source files** - the skill-specific addition: the file paths in the codebase where this processing is visible. This is what makes the skill output traceable and reviewable.

## Confidence indicators

The skill attaches a confidence indicator to inferred fields (HIGH / MEDIUM / LOW). These are presented inline in the relevant cell or as a separate confidence column for high-uncertainty fields. The convention is:

- **HIGH**: the code makes it explicit (e.g. a Prisma schema with an `email` field for the `User` model → "Categories of personal data: contact details (email)" is HIGH).
- **MEDIUM**: there is a strong contextual signal (e.g. a `/checkout` route handler calling Stripe → "Purpose: payment processing" is MEDIUM, since the signal is clear but the broader business purpose ("subscription billing for paid tier") needs human confirmation).
- **LOW**: a best guess (e.g. a `notifications` table with no clear feature mapping → "Purpose: user notifications" is LOW, since the actual purpose could be anything from transactional alerts to marketing).

Lawful basis is ALWAYS marked as a suggestion. Even where confidence is HIGH on the activity, the lawful basis determination is the controller's. Use the format: `Suggested: <basis> (confidence: <level>)`.

## Output format

The skill produces `ropa-draft.xlsx` only. Always start from `assets/ropa-template.xlsx` (which has the green-shaded mandatory columns, blue-shaded useful additional columns, instructions sheet, frozen panes, and correct column widths already in place), populate it via openpyxl, and save the result to `gdpr-review/ropa-draft.xlsx`.

Do not produce a CSV version of the ROPA. The green/blue colour coding carries practitioner meaning (legally mandatory under Article 30(1) vs ICO-recommended) that does not survive CSV. Drop-down lists, cell formatting, and the two-sheet structure also matter for usability and survive only in XLSX.

## Row examples

A typical small consumer app will produce 6-12 rows in the ROPA, grouped by business function and purpose. As a guide for what the ICO expects:

**Example row (consumer app, account management):**
- Business function: Account management
- Purpose: Provide and maintain user accounts so users can access the service they have signed up for
- Lawful basis: Suggested: Contract (confidence: HIGH)
- Categories of individuals: Registered users of the free tier; registered users of the paid tier
- Categories of personal data: Identity (name, email); authentication credentials (Apple ID identifier or hashed password); device data (device ID, app version)
- Categories of recipients: Auth0 (auth provider); Supabase (database hosting)
- Third countries: United States (Auth0 default region); region of Supabase project not visible in code
- Safeguards: UK Addendum to EU SCCs likely required for both
- Retention: UNKNOWN - no retention logic visible in code; reviewer to confirm against business need
- Security: TLS in transit (visible in API client config); encryption at rest at Supabase platform level
- Source files: `prisma/schema.prisma` (User model); `lib/auth.ts`; `app/api/auth/[...nextauth]/route.ts`

This level of detail per row is the target. If the skill is producing rows with one-word values, push for more specificity. If the skill is producing wall-of-text values, push for concision.
