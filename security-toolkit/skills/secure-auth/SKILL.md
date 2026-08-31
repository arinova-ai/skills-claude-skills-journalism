---
name: secure-auth
description: Secure authentication design and review using current OWASP, NIST, IETF, W3C, and platform guidance. Use for login, registration, password reset, sessions, tokens, OAuth, MFA, and passkeys.
---

# Secure authentication

Authentication guidance changes quickly. Treat the detailed recipes preserved
for audit in the companion's adjacent reference directory (file name
`upstream-secure-auth-details.md`) as a reviewed source snapshot, not as
timeless authority. That verbatim snapshot is deliberately outside the import
artifact because it contains a credential-shaped historical example.

## 1. Refresh the security baseline

Before recommending or implementing a pattern, check current primary sources
for the primitive in scope:

- NIST Digital Identity Guidelines and relevant supplements;
- OWASP Authentication, Session Management, Password Storage, and OAuth cheat
  sheets;
- current IETF RFCs and active security advisories for OAuth, JWT, and DPoP;
- W3C WebAuthn and FIDO Alliance passkey guidance;
- the chosen framework and library's official security documentation;
- CISA KEV, vendor advisories, and the relevant package registry for recent
  exploited vulnerabilities or changed defaults.

Record the date and links. If current authoritative guidance conflicts with the
snapshot, explain the drift and follow the current guidance. If browsing is not
available, state that limitation and avoid presenting version-sensitive values
as current facts.

## 2. Establish the threat model

Ask only for missing facts:

- application type, clients, domains, and trust boundaries;
- data sensitivity and required assurance level;
- account recovery, revocation, and logout requirements;
- federation or third-party identity providers;
- expected abuse, bot, phishing, credential-stuffing, and session-theft risks;
- regulatory and audit constraints;
- framework, identity service, deployment environment, and existing controls.

Do not request live passwords, tokens, recovery codes, private keys, browser
session state, or production credentials. Use placeholders in examples.

## 3. Choose the smallest suitable architecture

### Server-side sessions

Prefer an opaque, server-side session when the application has a conventional
web backend, immediate revocation matters, and a shared session store is
acceptable. Use a cryptographically random identifier in a secure cookie;
rotate it after authentication and privilege changes; enforce idle and absolute
expiry; validate CSRF protection; and revoke it on logout and recovery events.

### Signed access tokens

Use signed access tokens only when multiple services or non-browser clients
need independent verification. Keep them short-lived; validate issuer,
audience, algorithm, expiry, scope, and signing-key tenancy; design revocation
and refresh-token reuse detection; and avoid browser storage exposed to script.
For high-value APIs, assess sender-constrained tokens against current standards.

### Passkeys and MFA

Offer phishing-resistant WebAuthn/passkeys where platform support and assurance
requirements justify them. Plan enrollment, discoverable-credential behavior,
device loss, recovery, and migration before making them the only factor. Treat
SMS as a recovery or lower-assurance option, not the preferred strong factor.

## 4. Apply the control baseline

### Passwords

- Use the current OWASP-recommended password hashing algorithm and parameters;
  calibrate cost on the deployment hardware and document the result.
- Allow password managers, paste, and long passphrases. Check current NIST
  guidance before setting composition or forced-rotation rules.
- Compare breached-password indicators without logging the submitted password.
- Keep login responses and timing as uniform as practical to limit account
  enumeration.

### Sessions and cookies

- Use `Secure`, `HttpOnly`, and an intentional `SameSite` policy.
- Scope cookie domain and path narrowly; never place secrets in URLs.
- Rotate identifiers after authentication, MFA, privilege elevation, and
  account recovery.
- Bind risk decisions to observable signals without relying on brittle device
  fingerprinting as the only defense.
- Provide a way to review and revoke active sessions.

### Tokens and keys

- Pin allowed algorithms and reject algorithm confusion.
- Validate every relevant claim; do not treat successful signature parsing as
  full authorization.
- Separate signing keys by environment and tenant where the threat model
  requires it. Rotate through a documented, tested procedure.
- Store refresh tokens with protection appropriate to their value; detect reuse
  and revoke the affected token family.

### OAuth and federation

- Use Authorization Code with PKCE for interactive clients.
- Generate and verify state and nonce values as required by the protocol.
- Match redirect URIs exactly and keep them on trusted HTTPS origins.
- Request the minimum scopes and validate provider-specific issuer/audience
  rules against official documentation.
- Avoid an implicit or password-grant flow unless current standards and a
  documented legacy constraint explicitly require it.

### Password reset and account recovery

- Return non-enumerating responses.
- Issue single-use, high-entropy, short-lived recovery material; store only a
  protected verifier where practical.
- Invalidate or rotate affected sessions after a successful reset according to
  the threat model.
- Notify the account owner through an independent channel without exposing
  sensitive details.
- Require stronger recovery evidence for higher-assurance accounts and record
  auditable recovery events.

### Abuse resistance and observability

- Rate-limit by multiple dimensions and add progressive friction without
  creating a trivial denial-of-service primitive.
- Log security events, not secrets: success/failure class, risk decision,
  session or token family identifier, administrative action, and revocation.
- Alert on credential stuffing, impossible recovery patterns, MFA downgrade,
  refresh-token reuse, unusual key selection, and authorization anomalies.
- Define privacy-aware retention and access controls for authentication logs.

## 5. Produce an implementable result

Return:

1. assumptions and current-source evidence;
2. the selected architecture and rejected alternatives;
3. trust boundaries and abuse cases;
4. data model, cookie/token settings, and key lifecycle;
5. endpoint/state-flow design, including recovery and revocation;
6. framework-specific implementation notes with placeholder-only examples;
7. negative tests, concurrency tests, and security regression tests;
8. rollout, migration, monitoring, and rollback steps;
9. residual risks and decisions that still need a human owner.

## Verification checklist

- Unit and integration tests cover identifier rotation, expiry, revocation,
  reuse detection, CSRF, replay, malformed tokens, claim validation, and
  authorization boundaries.
- End-to-end tests cover enrollment, login, logout, recovery, MFA/passkey loss,
  and concurrent sessions.
- Logs contain no passwords, credentials, recovery material, raw session IDs,
  or full tokens.
- Security headers, cookie attributes, CORS, redirect URIs, and proxy/TLS
  assumptions are checked in the deployed topology.
- Dependency and advisory checks are pinned to the reviewed build and repeated
  before release.
- High-risk changes have an independent reviewer and a tested rollback path.

## Detailed source snapshot

Use the companion audit file named `upstream-secure-auth-details.md` when
reviewing the upstream implementation examples and extended rationale.
Revalidate every version number, dated claim, algorithm parameter, and
provider-specific recipe before applying it. The audit file is intentionally
outside the import artifact so the skill stays within the catalog's prompt-size
limit without deleting upstream substance or importing its credential-shaped
historical example.
