# Scanning strategy

How to scan a codebase efficiently. Aim for high recall on data flows and processors, not for reading every line.

## General approach

1. **Map the repo first**: take a directory listing (excluding standard noise: node_modules, .git, vendor, dist, build, .next, target, venv, __pycache__, .venv, .nuxt, public/static-assets). Identify the stack from top-level config files.

2. **Read manifests**: package.json, requirements.txt, Gemfile, go.mod, pom.xml, composer.json, pyproject.toml, Cargo.toml, *.csproj. These tell you what processors are in play.

3. **Read env templates**: .env.example, .env.sample, .env.template, env.d.ts, config files. These reveal services even when not in manifests (e.g. URLs, API key names).

4. **Identify data entry points**: where does user data come in?
   - API route handlers
   - Form components
   - GraphQL resolvers
   - Webhook receivers
   - File upload endpoints

5. **Identify data storage**: where does it go?
   - ORM schemas / migrations
   - Database init scripts
   - Cache config (Redis keys)
   - File storage SDK calls

6. **Identify data exits**: where does it leave?
   - Outbound HTTP clients
   - SDK calls to third parties
   - Email/SMS sends
   - Webhooks to external systems
   - Logging destinations

7. **Cross-cutting passes**:
   - Search for log statements with personal-looking variable names
   - Search for URL/route definitions with user data in path
   - Search for hardcoded secrets

## Stack-specific guidance

### Next.js / React / TypeScript

Key files:
- `package.json` - dependencies
- `prisma/schema.prisma` or `drizzle/schema.ts` - database schemas
- `app/api/**/route.ts` or `pages/api/**/*.ts` - API handlers
- `app/**/page.tsx` or `pages/**/*.tsx` - pages with forms
- `components/**/*.tsx` - form components
- `lib/auth.ts`, `auth.config.ts`, `middleware.ts` - auth
- `next.config.js` - rewrites, headers, third party domains
- `instrumentation.ts`, `sentry.client.config.ts` - observability

Form detection: look for `<form>` JSX, `<Input name="..." />`, `useForm` hooks, Zod schemas, React Hook Form usage.

### Python / Django

Key files:
- `requirements.txt`, `pyproject.toml`, `Pipfile` - dependencies
- `**/models.py` - database models
- `**/serializers.py` - DRF serialisers (data shape)
- `**/views.py`, `**/viewsets.py` - request handlers
- `**/urls.py` - routes
- `**/forms.py` - forms
- `settings/*.py` - middleware, installed apps, third party config
- `**/admin.py` - admin exposure

### Python / FastAPI / Flask

Key files:
- `requirements.txt`, `pyproject.toml`
- `**/main.py`, `**/app.py` - app entry
- `**/routers/*.py`, `**/blueprints/*.py` - route definitions
- `**/schemas.py`, `**/models.py` - Pydantic models / SQLAlchemy models
- `alembic/versions/*.py` - migrations

### Ruby on Rails

Key files:
- `Gemfile` - gems
- `db/schema.rb`, `db/migrate/*.rb` - schemas
- `app/models/*.rb` - models
- `app/controllers/*.rb` - controllers
- `config/routes.rb` - routes
- `config/initializers/*.rb` - third party initialisation
- `app/views/**/*.erb` - forms

### Go

Key files:
- `go.mod` - dependencies
- `**/handlers/*.go`, `**/api/*.go` - handlers
- `**/models/*.go`, `**/types/*.go` - structs (often with JSON/db tags)
- `**/migrations/*.sql` - schemas

### Java / Spring

Key files:
- `pom.xml`, `build.gradle` - dependencies
- `**/entity/*.java`, `**/model/*.java` - JPA entities
- `**/controller/*.java` - REST controllers
- `**/dto/*.java` - data transfer objects
- `application.properties`, `application.yml` - config
- `**/resources/db/migration/*.sql` - Flyway migrations

### Node.js / Express

Key files:
- `package.json`
- `**/routes/*.js`, `**/controllers/*.js`
- `**/models/*.js` - Mongoose schemas, Sequelize models
- `migrations/*.js`
- `app.js`, `server.js`, `index.js`

### PHP / Laravel

Key files:
- `composer.json`
- `app/Models/*.php`
- `app/Http/Controllers/*.php`
- `database/migrations/*.php`
- `routes/web.php`, `routes/api.php`
- `config/services.php` - third party

### Frontend-heavy (React/Vue/Angular only, no backend in repo)

Look for:
- API base URLs in env files (where does data go?)
- Direct third party SDK calls
- Analytics/tracking initialisations
- Cookie writes
- localStorage/sessionStorage usage with user data

Flag clearly that backend is out of scope and likely processes additional data.

## Patterns to search for

These regex/keyword searches are useful regardless of stack:

- `process.env.*KEY` or `os.environ['*KEY']` - secret references
- `console.log`, `print(`, `logger.`, `log.info`, `Log.d` - log statements (then check what's being logged)
- `fetch(`, `axios.`, `requests.`, `http.Get`, `RestTemplate` - outbound calls
- `<form`, `useForm`, `handleSubmit` - form handling
- `email`, `phone`, `password`, `dob`, `ssn`, `address` - field names

## Avoid these

- Don't read minified JS bundles (look for `.min.js`, large single-line files)
- Don't read lock files (package-lock.json, yarn.lock, poetry.lock) - duplicates manifest info
- Don't read test fixtures unless they reveal a data shape not visible elsewhere
- Don't read every component - focus on those with form inputs or data display
- Don't read .git contents

## Output stage

After scanning, before writing outputs:

1. Deduplicate findings (the same processor mentioned in 5 files is one entry)
2. Group personal data fields by entity/purpose, not by file location
3. Cross-reference processors against `references/processors.md`
4. For each unknown processor, mark UNKNOWN explicitly
5. Generate the methodology file last - it should describe what was actually scanned, not what was planned
