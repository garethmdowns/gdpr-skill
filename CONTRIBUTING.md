# Contributing

Thanks for considering a contribution. This skill is most useful when it is current and accurate, which is hard to do alone.

## What we particularly want

### Processor entries

The `references/processors.md` file is the most valuable part of the skill and the most likely to become stale. PRs that add or correct entries should include:

- The detection signal (import path, env var, URL pattern)
- The service name and provider
- Typical role (controller / processor / joint controller)
- Default data residency
- Available regions (especially EU/UK options)
- Notes on transfer mechanism (DPF certified? SCCs published? Adequate jurisdiction?)
- A source for the residency claim (provider documentation, trust centre URL, sub-processor list URL)

Do not add entries without a source. We would rather be silent than wrong.

### Stack-specific scanning

`references/scanning-strategy.md` covers common stacks but is not exhaustive. PRs welcome for:

- .NET / C#
- Rust web frameworks
- Elixir / Phoenix
- Mobile (Swift, Kotlin, React Native, Flutter)
- Static site generators with backend integrations

### Detection patterns

Improvements to detection patterns for:

- Special category data (health, biometrics, religious belief etc.)
- Children's data signals
- DSR-relevant endpoints
- PII in logs and URLs

### Test cases

Anonymised example codebases that the skill can be tested against, ideally with a known-good "answer key" of what the skill should find.

## What we do not want

- Entries with strong opinions on lawful basis. The skill suggests, with confidence indicators. Disagreements over lawful basis are for the reviewer.
- Entries that turn the skill into a compliance tool rather than a draft generator.
- Removing the "draft, for review" framing from outputs.

## Style

- UK English in all skill content (organisation, behaviour, etc.)
- No em dashes
- Plain language. The audience is a mix of developers and DPOs.
- Imperative form in skill instructions (the SKILL.md addresses Claude, not the user)

## Process

1. Open an issue first for anything beyond a small fix, so we can discuss scope.
2. Fork, branch, PR.
3. PRs that change processor entries should include a source.
4. PRs that change SKILL.md structure should include a brief note on why.

## Code of conduct

Be kind. This is a practitioner community project. Privacy people argue enough at conferences without us doing it here.
