# Personal data vs business contact data

This file explains the distinction between personal data of natural persons and information that relates only to legal entities. The skill must split listing, directory, supplier database, and contact register analysis into two portions where both are present, because UK GDPR applies to one and not the other.

## The basic position

UK GDPR applies to personal data. Personal data is any information relating to an identified or identifiable natural person (a living individual). Information that relates only to a legal entity (a limited company, a charity, a public body) is not personal data and is outside UK GDPR.

The line is not always clean. Many real-world data sets contain both.

## Worked examples

### Sole trader

A sole trader trading under their own name (e.g. "Jane Smith Plumbing", where Jane Smith is the proprietor) is a natural person. The trading name, business address, business phone, and business email all relate to Jane Smith. This is personal data and UK GDPR applies in full.

A sole trader trading under a non-name brand (e.g. "Riverside Plumbing", where the proprietor is still Jane Smith) is also a natural person. The brand does not change that. The trading address and contact details still relate to Jane Smith as the underlying individual.

### Limited company

A limited company is a legal person, not a natural person. The company name, registered office, company number, and a generic info@ inbox relate to the legal entity. UK GDPR does not apply to this information on its own.

Information about the natural persons connected to the company is personal data. This includes:

- A named director's name, work email, and work phone (personal data of that director).
- A named employee on a "meet the team" page (personal data of that employee).
- People with Significant Control records published under the Companies Act (personal data of those individuals, irrespective of whether they work at the company day-to-day).

### Mixed records

A typical B2B supplier directory entry might contain:

- Company name (legal entity, not personal data)
- Registered office address (legal entity, not personal data)
- Generic info@ inbox (legal entity, not personal data on its own)
- Named contact "Alex Brown, Operations Manager" with personal work email (personal data of Alex Brown)

The skill treats this as two portions: the legal entity portion (outside UK GDPR) and the personal data portion (inside UK GDPR).

## How the skill splits the analysis

Where a listing, directory, supplier database, or contact register contains both portions, the skill splits the analysis into two bullets in the relevant ROPA row or summary section:

1. **Personal data portion**: the named individuals, their work contact details, any biographical or role data. UK GDPR applies. Article 6 lawful basis required (typically Legitimate interests for B2B contact data, with a LIA, though Contract under Article 6(1)(b) may apply where there is a direct contractual relationship with the named individual's organisation). Article 21 right to object applies and must be surfaced in the privacy notice.

2. **Legal entity portion**: the company name, registered office, generic inboxes, public company-level information. UK GDPR does not apply. PECR may still apply if the controller uses this information for direct marketing by electronic mail. Regulation 23 sets identity and opt-out requirements for marketing to corporate subscribers (separate from the Regulation 22 consent requirement that applies to individual subscribers).

## Where the skill cannot tell from code alone

In most app codebases, the schema does not distinguish between a sole trader, a limited company, or a named contact at a limited company. A `providers` table with `name`, `address`, `email` could be any of these.

In this case, the skill flags the limitation explicitly. The ROPA row or summary should state:

`The codebase does not distinguish between sole traders and limited companies in this data set. Where providers are sole traders, partnerships of two or three, or include named individual contacts, UK GDPR applies (see the personal data portion above). Where providers are limited companies represented only by company-level information, UK GDPR does not apply (see the legal entity portion above). Reviewer to confirm the actual composition of the data set and apply the split accordingly.`

## PECR Regulations 22 and 23 - individual and corporate subscribers

For unsolicited marketing by electronic mail under PECR, the rules differ by subscriber type. Regulation 22 governs marketing to individual subscribers; Regulation 23 governs marketing to corporate subscribers:

- **Individual subscribers** (consumers, sole traders, non-LLP partnerships of two or three): default rule is consent, with the B2C soft opt-in available where the conditions are met. See `lawful-basis.md` for the PECR overlay detail.
- **Corporate subscribers** (limited companies, LLPs, certain partnerships, government bodies): Regulation 22 consent does not apply. Regulation 23 sets the separate requirement that the recipient must be given the sender's identity and the right to opt out in every message. Legitimate interests under UK GDPR can support the underlying processing of named individual contact data at the corporate subscriber.

The distinction matters even when UK GDPR does not apply to the entity itself, because PECR sits in its own statutory framework and operates regardless of whether the recipient is a natural person.

## What the skill does NOT do

- Does not classify specific providers in a data set as sole trader or limited company. That requires looking at Companies House or the controller's contract with the provider.
- Does not perform the Legitimate Interests Assessment for the personal data portion.
- Does not determine whether a partnership falls in the "individual subscriber" or "corporate subscriber" group under PECR. That depends on partnership type and constitution.
