# Suggesting lawful basis (and the PECR / AADC overlays)

This skill suggests lawful basis with explicit confidence indicators. Suggestions are ALWAYS framed as starting points for the reviewer, never as determinations.

UK GDPR Article 6 sits alongside two other regimes that affect lawful basis selection for many real-world activities:

- **PECR (Privacy and Electronic Communications Regulations 2003)** governs cookies and similar storage, electronic marketing, and traffic data. Where PECR applies, it overrides Article 6: even where a UK GDPR basis would work, PECR may require consent first.
- **Age Appropriate Design Code (AADC)** is the ICO's statutory code of practice for online services likely to be accessed by children. It applies regardless of whether children are the intended audience, if access is plausible.

The skill must surface all three layers where they apply. A suggestion of "Legitimate interests" without flagging the PECR overlay on product analytics is incomplete and misleading.

## The six lawful bases (Article 6 UK GDPR)

1. **Consent** - the data subject has given consent for one or more specific purposes
2. **Contract** - necessary for the performance of a contract with the data subject (or to take pre-contract steps at their request)
3. **Legal obligation** - necessary for compliance with a legal obligation
4. **Vital interests** - necessary to protect someone's life
5. **Public task** - necessary for a task in the public interest or exercise of official authority
6. **Legitimate interests** - necessary for the legitimate interests of the controller or a third party, except where overridden by the data subject's rights and interests

## How to suggest

For each processing activity identified, suggest the most likely basis based on the activity's purpose. Use confidence indicators:

- **HIGH**: the activity is clearly one of these textbook patterns
- **MEDIUM**: there is a strong signal but multiple bases could apply
- **LOW**: significant ambiguity; reviewer must determine

Always present as: `Suggested: <basis> (confidence: HIGH/MEDIUM/LOW) - <one sentence rationale>`.

### Dual-basis suggestions

For activities where two bases are both credible, suggest BOTH with reasoning. This is more honest than picking one and pretending the other doesn't exist. The pattern:

```
Suggested primary: Contract (confidence: MEDIUM) - the feature is presented as a core
service users sign up for.
Alternative: Legitimate interests (confidence: MEDIUM) - the underlying personalisation
is arguably an enhancement to a service that could function without it; choosing this
basis triggers Article 21 right-to-object obligations.
Reviewer to determine.
```

Activities where dual-basis suggestions are commonly appropriate:

- AI personalisation features (style profiles, recommendation engines, predictive features)
- Product analytics that inform feature decisions but aren't part of the core feature
- User profile features that go beyond what is strictly needed to deliver the service
- Long-term memory features (chat summaries, behavioural inference)
- Customer support records beyond what is strictly transactional

The test for distinguishing Contract from Legitimate interests in these cases: can the user enjoy the service the controller has advertised to them WITHOUT this specific processing? If yes, the basis is more likely Legitimate interests. If no, Contract is defensible.

## Common patterns and typical bases

### High confidence patterns (single basis)

- **User authentication / account management** → Contract (HIGH). Necessary to provide the service the user signed up for.
- **Order processing / payment** → Contract (HIGH). Necessary to fulfil the purchase.
- **Tax records / financial record-keeping** → Legal obligation (HIGH). Statutory retention requirements.
- **Fraud prevention / security logging** → Legitimate interests (HIGH). Always flag for LIA (Legitimate Interests Assessment).
- **Marketing email to existing customers (B2C soft opt-in)** → Legitimate interests + PECR Regulation 22 soft opt-in (HIGH where conditions met). The PECR conditions are: (a) the recipient was a customer or prospect for similar products/services obtained from the controller, (b) opt-out was given at the time of data collection, (c) opt-out is given in every subsequent message.
- **Marketing email to prospects / non-customers** → Consent under PECR Regulation 22 (HIGH). UK GDPR consent standard applies.
- **Special category data (Article 9)** → must ALSO have an Article 9 condition. Flag for explicit review.

### Patterns requiring dual-basis suggestion

- **AI features delivering recommendations** → Contract (if core) OR Legitimate interests (if enhancement). Confidence: MEDIUM on whichever is suggested primary.
- **Product analytics (first-party)** → Legitimate interests is the typical UK GDPR basis BUT the PECR overlay frequently requires consent (see PECR section). Suggest legitimate interests with the PECR consent flag.
- **Long-term memory / behavioural profiling** → Contract (if explicitly part of the advertised service) OR Legitimate interests (if implicit). Flag Article 22 if used for automated decisions.
- **Customer support records** → Contract (if part of the service contract) OR Legitimate interests (if broader QA / training).

### Patterns where the basis depends on purpose

- **AI / LLM processing of user content** → If necessary to deliver the feature: Contract. If for model training or improvement: very different analysis, likely Legitimate interests with significant scrutiny (LIA mandatory) or Consent. Distinguish in the ROPA.
- **Recommendation engines** → Could be Contract (service feature) or Legitimate interests (enhancement). Apply the "can the user enjoy the service without this?" test.
- **Data sharing with affiliates** → Depends on relationship, contracts, and notice given. Confidence: LOW.

### Listings, directories, and supplier databases

Listings, directories, supplier databases, and contact registers frequently contain a mix of personal data of natural persons (sole traders, partnerships of two or three, named contacts at corporate entities) and information that relates only to legal entities (limited company names, registered offices, generic info@ inboxes). UK GDPR applies to the personal data portion. It does not apply to the legal entity portion.

The skill must not collapse this into a single Legitimate interests analysis. It must split the lawful basis suggestion into two bullets in the relevant ROPA row or summary section:

- **Personal data portion**: Legitimate interests is the typical basis for B2B contact data, with a LIA. Contract under Article 6(1)(b) may apply where there is a direct contractual relationship with the named individual's organisation. Article 21 right to object applies and must be surfaced in the privacy notice starting points.
- **Legal entity portion**: UK GDPR does not apply. PECR may overlay if the controller uses the legal entity information for direct marketing by electronic mail. Regulation 23 sets identity and opt-out requirements for marketing to corporate subscribers (separate from the Regulation 22 consent requirement that applies to individual subscribers).

Where the codebase does not distinguish between sole traders, partnerships, and limited companies in the underlying schema, the skill flags this and directs the reviewer to confirm the actual composition of the data set. See `personal-data-vs-business-contact.md` for the full distinction, worked examples, and the PECR Regulations 22 and 23 treatment of individual and corporate subscribers.

## PECR overlay - when consent is required regardless of UK GDPR basis

PECR applies in addition to UK GDPR. Where PECR applies, its consent requirements operate independently of Article 6. Three areas matter for most app codebases:

### PECR Regulation 6 - storage of, or access to, information on terminal equipment

This is the "cookie law" but it is technology-neutral. It covers anything stored on, or accessed from, a user's device for any purpose that is not "strictly necessary" for the service the user has requested.

In practice, this catches:

- All non-essential cookies (analytics, advertising, personalisation, A/B testing)
- localStorage and sessionStorage values used for analytics or personalisation
- Mobile SDK data caches used for analytics (PostHog, Mixpanel, Amplitude, Heap, Sentry breadcrumbs, etc.)
- Tracking pixels and similar (Meta Pixel, LinkedIn Insight, TikTok Pixel)
- Fingerprinting and device identifiers used for tracking

The "strictly necessary" test is narrow. ICO guidance treats it as: necessary to provide a service the user has explicitly requested. Analytics for the controller's benefit fail this test even if "essential" to the controller's operations.

**When PECR Regulation 6 applies, prior consent is required.** This is true even where UK GDPR legitimate interests would otherwise support the processing.

The skill should flag:
- Any product analytics SDK detected (PostHog, Mixpanel, Amplitude, Heap, Plausible, Matomo where not self-hosted with anonymisation)
- Any advertising or attribution SDK (Meta Pixel, LinkedIn Insight, TikTok Pixel, Branch, AppsFlyer, Adjust)
- Any session replay tool (FullStory, LogRocket, Hotjar)
- Any A/B testing or feature flag service that profiles users (Optimizely, LaunchDarkly with user targeting, Statsig)

For these, the lawful basis suggestion in the ROPA should be: `Legitimate interests under UK GDPR Art 6(1)(f), BUT PECR Regulation 6 consent likely required for the SDK to operate at all. Confirm consent management is in place.`

### PECR Regulations 22 and 23 - unsolicited marketing by electronic mail

Covers email, SMS, in-app push notifications used for marketing purposes, and similar electronic marketing.

The default rule: consent required.

The B2C soft opt-in exception applies where ALL of these are true:
- Recipient was a customer or prospect for similar products/services obtained from the controller
- Opt-out was offered at the point of data collection
- Opt-out is offered in every subsequent message

For B2B email marketing to corporate subscribers, the rules differ. PECR Regulation 23 sets identity and opt-out requirements for corporate subscribers but does not require consent. Legitimate interests under UK GDPR is more often available for the underlying processing. In-app and SMS are treated the same as B2C.

**Push notifications need particular care.** Transactional push (the order has shipped, the cron job completed) is typically supportable on Contract or Legitimate interests. Push notifications that promote features, encourage re-engagement, or upsell premium tiers are likely electronic marketing and likely require PECR Regulation 22 consent.

The skill should flag any push notification logic that appears non-transactional (engagement nudges, "you haven't opened the app in X days", feature promotion, Premium upsell prompts) for PECR Regulation 22 review.

### PECR Regulation 5 - confidentiality of communications

Covers interception and monitoring of communications. Generally not relevant to typical app codebases unless the app processes communications between third parties. Flag if the codebase appears to do so.

## Article 9 (special category data)

If any processing involves or could ingest special category data:

1. Flag prominently in the AI processing file and in the relevant ROPA row
2. Do NOT suggest an Article 9 condition with HIGH confidence. The skill flags that ONE IS NEEDED.
3. The reviewer must determine which Article 9 condition applies (explicit consent, employment law, vital interests, manifestly made public, substantial public interest under Schedule 1 DPA 2018, etc.)

Ingest paths to watch for:
- Free-text input fields where users could volunteer health, religious belief, sexual orientation, political opinion, ethnic origin, etc.
- Image uploads that could reveal religious dress, disability, pregnancy, intimate context
- Voice and video features that could capture biometric voiceprint or facial features
- Health and fitness data (steps, sleep, heart rate, weight, cycle tracking)
- Dietary fields where they imply religious requirements (halal, kosher, etc.)

## Article 10 (criminal offence data)

Similar treatment to Article 9 but with narrower lawful conditions. Flag any DBS check field, conviction history, or similar.

## Article 21 (right to object)

Where lawful basis is Legitimate interests (or Public task), the data subject has an absolute right to object to direct marketing and a qualified right to object to other processing.

The skill must surface this in the ROPA row:
- Where Legitimate interests is suggested, the "Information rights process" column should note that Article 21 right-to-object applies
- The privacy notice starting points should include a "you have the right to object to [activity]" line for each Legitimate interests activity (see the "Privacy notice scaffolding" section of `SKILL.md` for where this section lives in the output)
- Where the activity is direct marketing, the right is absolute and the controller must stop processing on objection

## Article 22 (automated decision-making)

Article 22 protects against decisions based solely on automated processing that produce legal or similarly significant effects.

Most AI-assisted features in consumer apps do NOT engage Article 22 because the outputs are advisory (recommendations, suggestions, summaries) rather than determinative. The user retains agency.

Flag Article 22 review where the code suggests:
- Automated decisions on eligibility (loan approval, insurance pricing, employment screening)
- Automated decisions that disable, suspend, or restrict accounts
- Automated personalisation of pricing where the user cannot opt out
- Automated content moderation that has direct consequences for the user
- Profiling that determines access to features in ways the user cannot override

Where Article 22 may apply, the activity needs: explicit consent, contractual necessity, or authorisation by law, AND meaningful information about the logic, AND human-in-the-loop safeguards.

## Children's data and the Age Appropriate Design Code

The AADC applies to information society services (apps, websites, online services) "likely to be accessed by children" in the UK. Likelihood is assessed against the actual user base, not the intended audience. An app marketed to adults but accessible to children IS in scope.

The AADC sets 15 standards covering, among other things: best interests of the child, data minimisation, transparency for children, default privacy settings, profiling, nudge techniques, parental controls, and detrimental use of data.

The skill should flag AADC consideration in the methodology and summary outputs whenever:

- The app is publicly available without age verification on first launch
- The codebase contains no age-gate or age-affirmation flow
- The product surface (free download, gamification, social features, AI companion features) could plausibly attract under-18 users
- Marketing copy or app store positioning suggests teen relevance

**The AADC question is independent of Article 6.** An app with Contract as its lawful basis still needs to consider AADC if children are likely to access it.

UK GDPR Article 8 separately requires parental consent for processing based on Consent where the data subject is under 13 in the UK (or under 16 in some EEA jurisdictions). This applies only where Consent is the lawful basis, and only for information society services offered directly to children.

The skill should flag both: AADC scoping question, and Article 8 parental-consent question if Consent is the lawful basis.

## What the skill does NOT do

- Does NOT perform a Legitimate Interests Assessment (LIA). It only flags where one is likely needed.
- Does NOT confirm an Article 9 condition is in place for special category data.
- Does NOT validate consent collection mechanisms at runtime (it can only see the code, not the user's actual consent journey).
- Does NOT determine whether processing is "necessary" for the contract or for the legitimate interest. That is a controller judgement requiring business context.
- Does NOT confirm PECR compliance at runtime. It flags PECR applicability based on what the code suggests.
- Does NOT determine AADC scope formally. It flags signals.

## How findings should be presented

In the ROPA draft (the XLSX file), each row's "Lawful basis (suggested)" cell should follow this pattern:

**Single basis (high confidence):**
```
Suggested: Contract (Art 6(1)(b)) - confidence HIGH.
Rationale: account creation is necessary to provide the service users sign up for.
```

**Dual basis (medium confidence):**
```
Suggested primary: Contract (Art 6(1)(b)) - confidence MEDIUM.
Alternative: Legitimate interests (Art 6(1)(f)) - confidence MEDIUM.
Rationale: AI personalisation could be argued either way. Reviewer to determine.
LIA required if Legitimate interests is selected.
```

**PECR overlay (consent required regardless of UK GDPR basis):**
```
Suggested UK GDPR basis: Legitimate interests (Art 6(1)(f)) - confidence MEDIUM.
PECR Regulation 6 applies: consent likely required for SDK storage on user device.
Confirm consent management is in place before relying on Legitimate interests.
```

In the summary, group activities by suggested basis with a note that the suggestions are starting points and that PECR/AADC overlays may apply.
