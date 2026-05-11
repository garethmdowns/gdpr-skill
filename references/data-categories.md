# Personal data categories - taxonomy and detection patterns

This is the canonical taxonomy used by the GDPR skill. When a field is detected, map it to one of these categories. Confidence indicators apply: a field literally named `email` is HIGH confidence; a field named `contact` requires looking at usage to determine MEDIUM/LOW confidence.

## Standard personal data

### Identity and contact
- **Name**: first_name, last_name, full_name, surname, given_name, display_name, username, handle, screen_name
- **Email**: email, email_address, user_email, contact_email, work_email, personal_email
- **Phone**: phone, telephone, mobile, cell, phone_number, contact_number, sms_number
- **Postal address**: address, street, city, postcode, postal_code, zip, zipcode, country, address_line_1, address_line_2
- **Date of birth**: dob, date_of_birth, birthdate, birth_date, birthday
- **Age**: age, age_group, age_range (flag if children possible)
- **Gender**: gender, sex, pronoun, pronouns
- **National ID**: nino, ni_number, ssn, national_id, passport, passport_number, driving_licence (special handling, flag as high sensitivity)

### Authentication and account
- **Username**: username, login, user_id (may be PII depending on context)
- **Password**: password, pwd, passwd, password_hash, hashed_password (should be hashed; flag if appears in plaintext patterns)
- **Security questions**: security_question, security_answer, recovery_question
- **MFA secrets**: totp_secret, mfa_secret, backup_codes
- **Tokens**: access_token, refresh_token, api_key, session_token (flag if logged or returned in responses)

### Location data
- **IP address**: ip, ip_address, remote_ip, client_ip, x_forwarded_for
- **Coordinates**: latitude, longitude, lat, lng, geo, location, gps
- **Address derived**: country, region, timezone (when tied to a user)

### Device and technical
- **Device identifiers**: device_id, advertising_id, idfa, gaid, mac_address, imei
- **Browser fingerprint**: user_agent, browser, os, screen_resolution, fingerprint, canvas_hash
- **Cookies**: any cookie storing user state, session_id, _ga, _fbp, fr (Meta), etc.

### Financial
- **Payment cards**: card_number, pan, card_token, last_four, last4, cvv, cvc, expiry (flag PCI scope)
- **Bank**: account_number, sort_code, iban, bic, swift, routing_number
- **Income/wealth**: salary, income, net_worth, assets, credit_score
- **Transactions**: transaction, payment, invoice, order, purchase (with user link)
- **Tax**: tax_id, vat_number, utr (if personal trader)

### Behavioural and inferred
- **Activity**: clicks, views, page_views, sessions, events, interactions
- **Preferences**: preferences, interests, segments, audience, persona
- **Profile**: profile, bio, about, description (user-generated)
- **Search history**: search, query, searches, search_history
- **Purchase history**: order_history, purchases, basket
- **Inferred attributes**: predicted_*, scored_*, _segment, _persona, lifecycle_stage

### Communications content
- **Messages**: message, chat, conversation, dm, direct_message, comment, reply
- **Email content**: email_body, email_content, subject, ticket_body
- **Voice/call**: call_recording, transcript, voicemail, recording_url
- **Uploaded content**: upload, attachment, file_url, document, photo, image (when user-attributed)

### Employment (when applicable)
- **Job**: job_title, role, company, employer, department
- **Performance**: performance_rating, review, appraisal
- **Compensation**: salary, bonus, commission (overlap with financial)

## Special category data (Article 9 UK GDPR)

Flag any of these with HIGH SENSITIVITY. Requires Article 9 condition for processing.

- **Health**: health, medical, condition, diagnosis, prescription, doctor, gp, hospital, allergy, disability, mental_health, pregnancy, blood_type
- **Racial/ethnic origin**: race, ethnicity, ethnic, nationality (in some contexts)
- **Religious/philosophical belief**: religion, faith, belief, dietary (where it implies religious requirement, e.g. halal, kosher)
- **Trade union membership**: union, union_member, trade_union
- **Sexual orientation**: orientation, sexuality, lgbtq, gay, lesbian, bi, trans (where collected as data)
- **Sex life**: any explicit sexual behaviour data
- **Biometric**: fingerprint, face_id, voice_print, retina, iris, biometric_template
- **Genetic**: dna, genetic, gene, genome
- **Political opinion**: political, party, voting, political_view

## Criminal offence data (Article 10)

- conviction, offence, criminal_record, dbs, dbs_check, crb, criminal_history, sentence, caution

## Children's data signals

Flag if any of these appear:
- Fields suggesting collection from minors: age fields where age can be < 18, school, school_year, parent_consent, guardian_email
- Marketing copy in repo (READMEs, content files) targeting children or teens
- Age gates that allow under-13s (US COPPA) or under-13/16s (UK GDPR Article 8)
- Sign-up flows without age verification on services likely to be used by children

## Pseudonymous but still personal

Per Recital 26 UK GDPR, pseudonymous data is still personal data. Flag:
- user_id, customer_id, account_id when reasonably linkable
- Hashed identifiers (hashed_email, sha256(email)) - still personal data
- Persistent identifiers (device_id, session_id over time)

## What is NOT personal data

Help reduce false positives. The following are generally not personal data on their own:
- System identifiers (server_id, instance_id, container_id)
- Internal references (sku, product_id, category_id) unless tied to a user
- Aggregated/anonymised counters where re-identification is implausible
- Static content (page titles, marketing copy, FAQs)

If in doubt, flag and let the reviewer decide.
