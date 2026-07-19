# Compliant Fetching

`feed-all` uses a conservative, RSS-first access policy.

## Rules

- Prefer RSS or Atom over scraping HTML.
- Identify the client with a stable User-Agent.
- Respect robots.txt, site terms, access controls, and rate limits.
- Use conditional requests and honor `Retry-After` where implemented.
- Revalidate redirect targets and keep response-size and SSRF limits.
- Stop on CAPTCHA, login, paywall, or anti-bot challenge pages.
- Treat 403 and 401 as authorization failures, not as prompts to bypass access.
- Classify 429 and 503 as retryable only according to server guidance.

Robots rules are a crawler signal, not an authorization substitute. Legal and
contractual obligations depend on the source and use case; this document is an
engineering policy, not legal advice.

## Source Health

Agents should preserve the source URL, last status, error reason, and whether a
source was manually disabled. A failed source should be diagnosed or replaced,
not hammered repeatedly.

## Out of Scope

Stealth crawling, CAPTCHA solving, credential reuse, proxy rotation intended to
evade controls, and scraping private or paid content without authorization.
