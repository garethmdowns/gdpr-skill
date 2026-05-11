# Third party processor lookup

This table identifies common third party services that appear in code, their typical role, default data residency, and applicable transfer mechanisms. It is a snapshot and should be refreshed periodically. When in doubt, flag for review.

For each entry: detection signals → service name → typical role → default residency → transfer mechanism → notes.

## Analytics and product analytics

- `_ga`, `gtag`, `googletagmanager`, `google-analytics`, `analytics.google.com` → **Google Analytics** → processor (typically) → US → DPF / SCCs → EU regions available (GA4 EU); flag for IP anonymisation review
- `mixpanel`, `mixpanel-browser`, `MIXPANEL_TOKEN` → **Mixpanel** → processor → US → SCCs → EU residency available on Enterprise
- `amplitude`, `@amplitude/analytics-browser` → **Amplitude** → processor → US → SCCs → EU residency available
- `posthog`, `posthog-js`, `posthog-node` → **PostHog** → processor → US/EU (configurable) → SCCs if US → Self-hosted option exists
- `segment`, `@segment/analytics-node`, `analytics.js` → **Segment (Twilio)** → processor → US → SCCs → Acts as data pipeline to many other processors
- `heap`, `heapanalytics` → **Heap** → processor → US → SCCs
- `hotjar`, `_hjSettings` → **Hotjar** → processor → EU (Ireland) → no transfer (typically)
- `fullstory`, `fs.js` → **FullStory** → processor → US → SCCs → Session replay - high sensitivity, flag for review
- `logrocket` → **LogRocket** → processor → US → SCCs → Session replay - high sensitivity, flag for review

## Advertising and marketing

- `fbq`, `_fbp`, `connect.facebook.net`, `meta-pixel` → **Meta Pixel** → joint controller (per CJEU Fashion ID) → US → DPF/SCCs → Joint controller status changes ROPA/notice obligations
- `gtag('event', ...)` with `send_to: 'AW-...'` → **Google Ads** → joint controller → US → DPF/SCCs
- `linkedin_insight`, `_linkedin_data_partner_id` → **LinkedIn Insight Tag** → joint controller → US/IE → DPF/SCCs
- `ttq`, `tiktok-pixel` → **TikTok Pixel** → joint controller → US/SG/IE → SCCs → Data also flows to China per TikTok policies (heightened scrutiny)
- `klaviyo` → **Klaviyo** → processor → US → SCCs
- `mailchimp`, `MAILCHIMP_API_KEY` → **Mailchimp** → processor → US → DPF/SCCs
- `sendgrid`, `@sendgrid/mail` → **SendGrid (Twilio)** → processor → US → SCCs
- `mailgun` → **Mailgun** → processor → US/EU (configurable) → SCCs if US
- `postmark`, `postmarkapp` → **Postmark** → processor → US → SCCs → EU region available
- `hubspot`, `@hubspot/api-client` → **HubSpot** → processor → US/EU (configurable) → SCCs if US
- `intercom`, `intercom-client` → **Intercom** → processor → US/EU (configurable) → SCCs if US
- `customer.io` → **Customer.io** → processor → US/EU (configurable) → SCCs if US

## Payments

- `stripe`, `@stripe/stripe-js`, `STRIPE_SECRET_KEY` → **Stripe** → processor → US (with EU sub-processors) → SCCs → PCI scope; flag card data handling
- `paypal`, `@paypal/checkout-server-sdk` → **PayPal** → controller (for the payment) → US → DPF/SCCs → Paypal is independent controller for the transaction
- `braintree` → **Braintree (PayPal)** → processor → US → SCCs
- `adyen` → **Adyen** → processor → Netherlands (EU) → no transfer → Good GDPR position
- `square`, `@square/web-sdk` → **Square** → processor → US → SCCs
- `gocardless` → **GoCardless** → processor → UK/EU → no transfer
- `klarna` → **Klarna** → processor → Sweden (EU) → no transfer

## Error tracking and observability

- `sentry`, `@sentry/node`, `@sentry/browser`, `SENTRY_DSN` → **Sentry** → processor → US (with EU region available) → SCCs if US → User context may include PII; flag for review
- `datadog`, `dd-trace`, `DD_API_KEY` → **Datadog** → processor → US/EU (configurable) → SCCs if US
- `newrelic`, `NEW_RELIC_LICENSE_KEY` → **New Relic** → processor → US/EU → SCCs if US
- `bugsnag` → **Bugsnag (SmartBear)** → processor → US → SCCs
- `rollbar` → **Rollbar** → processor → US → SCCs
- `honeycomb` → **Honeycomb** → processor → US/EU → SCCs if US

## Customer support

- `zendesk` → **Zendesk** → processor → US/EU (configurable) → SCCs if US
- `freshdesk` → **Freshdesk (Freshworks)** → processor → US/EU/IN (configurable) → SCCs/Adequacy → India is not adequate
- `helpscout` → **Help Scout** → processor → US → SCCs
- `crisp.chat` → **Crisp** → processor → France (EU) → no transfer
- `tawk.to` → **Tawk.to** → processor → US → SCCs
- `drift` → **Drift** → processor → US → SCCs

## Cloud infrastructure

- `aws-sdk`, `@aws-sdk/*`, `AWS_REGION` → **AWS** → processor → depends on region (eu-west-1, eu-west-2 = EU/UK) → SCCs if non-EU region → CHECK REGION
- `@google-cloud/*`, `GOOGLE_APPLICATION_CREDENTIALS` → **Google Cloud** → processor → depends on region → SCCs if non-EU
- `@azure/*`, `AZURE_*` → **Azure** → processor → depends on region → SCCs if non-EU
- `cloudflare`, `cf-ray` → **Cloudflare** → processor → US → SCCs → CDN, also processes IP addresses
- `fastly` → **Fastly** → processor → US → SCCs
- `vercel`, `@vercel/*` → **Vercel** → processor → US (with EU regions) → SCCs if US → Edge functions may run globally
- `netlify` → **Netlify** → processor → US → SCCs
- `digitalocean` → **DigitalOcean** → processor → depends on region → SCCs if non-EU

## Databases and storage

- `supabase`, `@supabase/supabase-js`, `SUPABASE_URL` → **Supabase** → processor → depends on region → SCCs if non-EU → CHECK PROJECT REGION
- `mongodb+srv://*.mongodb.net`, `mongodb-atlas` → **MongoDB Atlas** → processor → depends on region → SCCs if non-EU
- `planetscale`, `@planetscale/database` → **PlanetScale** → processor → US (regions available) → SCCs if US
- `neon`, `@neondatabase/serverless` → **Neon** → processor → US/EU → SCCs if US
- `firebase`, `firebase-admin`, `FIREBASE_*` → **Firebase (Google)** → processor → US (configurable) → SCCs if US
- `airtable` → **Airtable** → processor → US → SCCs
- `notion`, `@notionhq/client` → **Notion** → processor → US → SCCs (limited GDPR maturity historically)

## Authentication

- `auth0`, `@auth0/auth0-react`, `AUTH0_DOMAIN` → **Auth0 (Okta)** → processor → US/EU (configurable) → SCCs if US
- `clerk`, `@clerk/*`, `CLERK_*` → **Clerk** → processor → US → SCCs
- `firebase-auth` → **Firebase Auth** → processor → US → SCCs
- `nextauth`, `next-auth` → **NextAuth.js (Auth.js)** → not a service (library) → N/A → depends on configured provider
- `okta` → **Okta** → processor → US → SCCs
- `workos`, `@workos-inc/node` → **WorkOS** → processor → US → SCCs

## AI and ML

- `openai`, `@openai/api`, `OPENAI_API_KEY` → **OpenAI** → processor → US → SCCs → API: no training by default since March 2023; ChatGPT consumer: different terms. Flag for review.
- `anthropic`, `@anthropic-ai/sdk`, `ANTHROPIC_API_KEY` → **Anthropic** → processor → US → SCCs → API: no training by default; trust centre details available
- `cohere`, `cohere-ai` → **Cohere** → processor → US/Canada → SCCs (Canada has adequacy)
- `mistralai`, `@mistralai/mistralai` → **Mistral** → processor → France (EU) → no transfer → Good GDPR position
- `groq`, `groq-sdk` → **Groq** → processor → US → SCCs
- `together`, `together-ai` → **Together AI** → processor → US → SCCs
- `replicate` → **Replicate** → processor → US → SCCs
- `huggingface`, `@huggingface/*`, `HF_TOKEN` → **Hugging Face Inference** → processor → US/EU → SCCs if US
- `bedrock`, `BedrockRuntime` → **AWS Bedrock** → processor → depends on AWS region → SCCs if non-EU
- `@google-cloud/vertexai` → **Vertex AI** → processor → depends on region → SCCs if non-EU
- `azure-openai` → **Azure OpenAI** → processor → depends on Azure region → SCCs if non-EU → Often chosen for EU residency

## Vector databases and embeddings

- `pinecone`, `@pinecone-database/pinecone` → **Pinecone** → processor → US/EU (configurable) → SCCs if US
- `weaviate`, `weaviate-client` → **Weaviate** → processor → self-hosted or cloud (EU available)
- `qdrant`, `@qdrant/js-client-rest` → **Qdrant** → processor → self-hosted or cloud → CHECK
- `chromadb`, `chroma` → **Chroma** → typically self-hosted; flag if cloud variant detected
- `milvus`, `pymilvus` → **Milvus** → typically self-hosted; Zilliz Cloud is US/EU

## Speech and vision

- `whisper`, `openai-whisper` → **OpenAI Whisper** (API) → see OpenAI
- `assemblyai` → **AssemblyAI** → processor → US → SCCs
- `deepgram`, `@deepgram/sdk` → **Deepgram** → processor → US → SCCs
- `elevenlabs` → **ElevenLabs** → processor → US → SCCs
- `@google-cloud/vision` → **Google Vision API** → see Google Cloud
- `@azure/ai-vision-*` → **Azure Computer Vision** → see Azure

## Productivity and collaboration (often processor for content)

- `slack`, `@slack/web-api` → **Slack (Salesforce)** → processor → US (EU residency available on Enterprise+) → SCCs if US
- `microsoft-graph`, `@microsoft/microsoft-graph-client` → **Microsoft Graph** → processor → depends on tenant region → SCCs if non-EU
- `googleapis` (Drive, Calendar, Gmail) → **Google Workspace** → processor → US (EU residency available) → SCCs if US
- `dropbox` → **Dropbox** → processor → US → SCCs
- `box` → **Box** → processor → US (EU available) → SCCs if US

## SMS and voice

- `twilio` → **Twilio** → processor → US (EU available) → SCCs if US
- `vonage` → **Vonage** → processor → US/UK → SCCs if US
- `messagebird` → **MessageBird** → processor → Netherlands (EU) → no transfer

## Maps

- `@googlemaps/*` → **Google Maps** → processor → US → SCCs (also processes IP for the user)
- `mapbox`, `mapbox-gl` → **Mapbox** → processor → US → SCCs
- `here.com` → **HERE** → processor → Germany (EU) → no transfer

## Notes on the table

This is not exhaustive. New SDKs appear constantly. For unknown packages:

1. Note the package name and what it appears to do
2. Flag as "UNKNOWN - manual review required"
3. Suggest the user check: provider data residency, sub-processors list, presence of DPA/SCCs/IDTA, default vs configurable region

Some packages are libraries with no data flow (e.g. `lodash`, `dayjs`, `zod`). Don't flag these.

For SDKs that wrap multiple providers (e.g. LangChain), the SDK itself is not the processor - the underlying model provider is. Trace to the actual API call.

## UK transfer mechanisms (as of skill version)

- **Adequacy regulations**: countries the UK has determined adequate (EEA, Andorra, Argentina, Canada (commercial), Faroe Islands, Guernsey, Israel, Isle of Man, Japan (private sector), Jersey, New Zealand, South Korea, Switzerland, Uruguay)
- **UK-US Data Bridge / DPF**: for US recipients certified to the Data Privacy Framework
- **UK IDTA (International Data Transfer Agreement)** or **UK Addendum to EU SCCs**: for transfers to non-adequate countries without DPF certification
- **BCRs (Binding Corporate Rules)**: for intra-group transfers
- **Article 49 derogations**: limited exceptions

Always note: the code cannot tell us which mechanism is contractually in place. The output flags WHAT is needed, not WHAT is.
