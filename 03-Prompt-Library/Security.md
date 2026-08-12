# Security

> **AI Engineering Workbench — Version 1**  
> Production-grade reusable prompts for Java 21, Spring Boot 3.x, Spring Security 6, Gradle, and modern cloud-native systems.

## How to Use This Chapter

These prompts are designed for context-aware AI engineering agents that already have access to the current repository, workspace, selected resources, configuration, logs, manifests, or authorized read-only cloud context. Each prompt asks the agent to identify risks before recommending fixes, classify findings as **Critical**, **High**, **Medium**, or **Low**, explain tradeoffs, prefer incremental remediation, and validate changes by compiling and running relevant tests whenever practical.

---

## Security Architecture

### 1. End-to-End Application Security Architecture Review

**Purpose:** Evaluate the application's security architecture as a coherent system rather than as isolated controls.

**Context:** A Java 21 / Spring Boot 3.x application using Spring Security 6 and production infrastructure.

**When to use it:** Before a major release, architecture review, migration, or security hardening initiative.

**Complete prompt:**

```text
Act as a senior application security architect. Review the current repository and its deployment configuration end to end.

Evaluate trust boundaries, authentication, authorization, identity propagation, API exposure, data protection, secret handling, dependency risk, network controls, persistence security, observability, administrative access, and production deployment assumptions.

Start by reconstructing the effective security architecture from the implementation. Identify risks before proposing fixes. Classify every material finding as Critical, High, Medium, or Low and explain the attack scenario, affected assets, prerequisites, and likely impact.

For each recommendation, explain the architectural rationale and security tradeoffs. Prefer incremental changes over unnecessary rewrites. Distinguish application-layer controls from infrastructure-layer controls and identify places where the design relies on undocumented assumptions.

Where changes are appropriate, implement the smallest safe set, preserving existing behavior unless that behavior is insecure. Use Java 21, Spring Boot 3.x, Spring Security 6, Gradle, constructor injection, and Jakarta APIs where applicable. Include explicit imports in Java changes. Compile the project and run relevant automated tests after changes.

Return:
1. Reconstructed security architecture.
2. Trust boundaries and protected assets.
3. Prioritized findings table.
4. Recommended remediation sequence.
5. Changes made, if requested or clearly safe.
6. Validation performed.
7. Residual risks and decisions requiring human approval.
```

**Expected AI output:** A prioritized architecture assessment with concrete attack paths, remediation rationale, and validated incremental changes where appropriate.

**Optional follow-up prompts:**
- `Convert the findings into a remediation backlog ordered by exploitability, blast radius, and implementation effort.`
- `Produce a target-state security architecture that resolves the High and Critical findings without changing the application's core domain design.`

**Notes or cautions:** Do not treat the presence of a security framework as evidence that controls are correctly configured.

### 2. Trust Boundary and Data Flow Security Analysis

**Purpose:** Discover security assumptions hidden in application and infrastructure boundaries.

**Context:** A distributed or layered application with external clients, services, databases, queues, or cloud resources.

**When to use it:** When architecture diagrams are incomplete, outdated, or do not capture actual runtime trust relationships.

**Complete prompt:**

```text
Analyze the current repository and deployment configuration to derive the application's security-relevant data flows and trust boundaries.

Identify external entry points, internal service calls, asynchronous messaging, database access, object storage, third-party APIs, identity providers, administrative interfaces, and infrastructure control planes. For each boundary, determine what identity is presented, what credentials or tokens cross it, what authorization decision is made, what data crosses it, and whether transport and stored data are protected appropriately.

Flag implicit trust, unauthenticated internal traffic, shared credentials, overly broad network access, missing authorization at downstream boundaries, and cases where a perimeter control is being used as a substitute for application authorization.

Classify findings as Critical, High, Medium, or Low. Explain realistic attack paths before recommending changes. Prefer defense in depth and incremental remediation. Where practical, verify conclusions against code and configuration rather than relying on naming conventions.

Return a concise textual data-flow model, trust-boundary matrix, prioritized findings, recommended controls, and unresolved assumptions that require confirmation.
```

**Expected AI output:** A trust-boundary matrix and evidence-based assessment of identity, authorization, and data protection across flows.

**Optional follow-up prompts:**
- `Focus only on service-to-service trust boundaries and identify where zero-trust assumptions are violated.`
- `Turn the trust-boundary analysis into a threat-modeling input document.`

### 3. Security Control Ownership Review

**Purpose:** Detect controls that are missing because teams assume another layer provides them.

**Context:** Applications deployed behind gateways, load balancers, service meshes, WAFs, Kubernetes, or cloud IAM.

**When to use it:** When security responsibilities are split between application and platform teams.

**Complete prompt:**

```text
Review the current application and deployment configuration and map each important security control to the layer that actually enforces it.

Cover authentication, authorization, rate limiting, TLS termination, request-size limits, CORS, CSRF, input validation, network isolation, secret access, audit logging, encryption, certificate validation, dependency scanning, container controls, and administrative access.

Identify controls that are duplicated, contradictory, absent, or assumed to exist in another layer. Pay particular attention to controls enforced only at the edge that can be bypassed by internal or alternate routes.

Classify material gaps as Critical, High, Medium, or Low. Explain the failure mode and attack path before recommending remediation. Clearly distinguish controls that should remain centralized from controls that must also be enforced in the application.

Return a control-ownership matrix, prioritized gaps, recommended ownership model, and incremental remediation plan.
```

**Expected AI output:** A security control matrix showing enforcement points, gaps, and dangerous cross-team assumptions.

**Optional follow-up prompts:**
- `Identify which controls must be tested automatically in CI/CD and propose those tests.`

---

## Threat Modeling

### 4. Repository-Derived Threat Model

**Purpose:** Generate a threat model from the implementation rather than from a generic checklist.

**Context:** Any production application or service.

**When to use it:** During design reviews, major feature work, or before external exposure.

**Complete prompt:**

```text
Create an implementation-grounded threat model for the current system.

Inspect the repository and deployment configuration to identify assets, actors, entry points, trust boundaries, privileged operations, sensitive data, external dependencies, and administrative paths. Use STRIDE as a coverage technique, not as a substitute for engineering judgment.

For each credible threat, describe the attacker goal, prerequisites, attack path, existing controls, control weaknesses, business or technical impact, and recommended mitigation. Do not invent vulnerabilities merely to fill STRIDE categories.

Classify findings as Critical, High, Medium, or Low based on realistic exploitability and impact. Prioritize threats that cross trust boundaries, affect identity or authorization, expose sensitive data, enable code execution, or expand blast radius.

Recommend incremental mitigations and explain security/usability/operability tradeoffs. Identify threats accepted by design and assumptions that need validation.

Return the system model, prioritized threat register, mitigation plan, and residual risks.
```

**Expected AI output:** A credible, implementation-specific threat register rather than generic STRIDE boilerplate.

**Optional follow-up prompts:**
- `Focus the threat model on authenticated malicious users rather than anonymous attackers.`
- `Reassess the model assuming one internal service is compromised.`

### 5. New Feature Threat Modeling

**Purpose:** Assess the security impact of a feature before or during implementation.

**Context:** A feature branch or current work introducing new endpoints, data flows, permissions, integrations, or storage.

**When to use it:** Before merging security-sensitive functionality.

**Complete prompt:**

```text
Threat-model the current feature changes in the context of the existing system.

Determine what new assets, entry points, permissions, trust relationships, data flows, dependencies, and failure modes the feature introduces. Compare the feature with the baseline architecture and identify security properties that have changed.

Analyze abuse cases, privilege escalation, authorization bypass, data leakage, replay, injection, resource exhaustion, unsafe defaults, and operational misuse where relevant.

Classify findings as Critical, High, Medium, or Low. Explain the risk before suggesting a fix. Prefer controls that fit existing architecture and avoid broad redesign unless the feature creates an architectural security flaw.

Recommend tests that demonstrate both permitted and denied behavior. If code changes are appropriate, make incremental changes, compile, and run relevant tests.
```

**Expected AI output:** A delta-focused threat assessment with targeted mitigations and security tests.

**Optional follow-up prompts:**
- `Generate abuse-case tests for the three highest-risk threats.`

---

## Authentication

### 6. Authentication Flow Review

**Purpose:** Verify that the actual authentication flow is secure from entry point through SecurityContext creation.

**Context:** Spring Security 6 application using sessions, OAuth/OIDC, JWTs, API keys, or another mechanism.

**When to use it:** When authentication behavior is unclear, changing, or failing security review.

**Complete prompt:**

```text
Review the current authentication implementation as a senior Spring Security engineer and application security architect.

Trace each supported authentication flow from the incoming request through credential or token validation to creation of the Spring Security Authentication and SecurityContext. Identify the filters, providers, converters, handlers, token validators, session behavior, and identity-provider interactions involved.

Evaluate credential handling, token validation, issuer and audience checks, algorithm restrictions, replay exposure, error handling, account state checks, session fixation, remember-me behavior, and fallback authentication paths.

Identify risks before fixes and classify them as Critical, High, Medium, or Low. Explain whether each issue can cause impersonation, authentication bypass, credential theft, or session compromise.

Recommend incremental changes that align with Spring Security 6. Avoid custom authentication code where framework-supported mechanisms are safer. If modifying Java code, use explicit imports and constructor injection. Compile and run authentication tests after changes.
```

**Expected AI output:** A traceable authentication flow, prioritized weaknesses, and framework-aligned remediation.

**Optional follow-up prompts:**
- `Create integration tests proving invalid issuer, invalid audience, expired token, and unsigned token rejection.`

### 7. Multi-Factor Authentication Integration Review

**Purpose:** Assess whether MFA is correctly enforced for the operations that require it.

**Context:** An application relying on an external IdP or custom step-up authentication.

**When to use it:** For administrative, financial, sensitive-data, or high-risk operations.

**Complete prompt:**

```text
Review how the current system integrates multi-factor or step-up authentication.

Determine where MFA is enforced, how the application learns that MFA occurred, whether that assertion is cryptographically trustworthy, and whether sensitive operations can be reached with a lower assurance level.

Inspect OIDC claims, authentication context, authorization rules, session lifetime, token refresh behavior, and privileged workflows. Identify downgrade paths, stale sessions, claim-mapping errors, and places where UI behavior is mistaken for server-side enforcement.

Classify findings as Critical, High, Medium, or Low. Explain risks before remediation. Recommend the smallest architecture-consistent improvements and describe user-experience tradeoffs.

Return current enforcement model, gaps, recommended assurance policy, and tests that prove step-up requirements cannot be bypassed.
```

**Expected AI output:** An assurance-level analysis tied to concrete privileged operations.

**Optional follow-up prompts:**
- `Implement server-side enforcement for the highest-risk operation and add positive and negative tests.`

---

## Authorization

### 8. Authorization Coverage Review

**Purpose:** Find endpoints and operations that are authenticated but insufficiently authorized.

**Context:** Spring Boot REST or web application.

**When to use it:** During security review or after adding new endpoints.

**Complete prompt:**

```text
Review authorization across the current Spring Boot application.

Inventory externally reachable endpoints and privileged service operations. Determine the effective authorization requirement for each one by examining SecurityFilterChain rules, method security, custom authorization managers, domain checks, and any gateway-level controls.

Identify missing authorization, overly broad authenticated-user access, rule-ordering mistakes, inconsistent endpoint and method rules, insecure defaults, and operations whose authorization depends only on client-supplied identifiers.

Classify findings as Critical, High, Medium, or Low. Explain the unauthorized action an attacker could perform before proposing changes. Distinguish coarse-grained endpoint authorization from object-level/domain authorization.

Prefer deny-by-default behavior and incremental changes. Add or update tests for both allowed and denied access. Compile and run the relevant test suite.
```

**Expected AI output:** Endpoint/operation authorization coverage with explicit gaps and validated fixes.

**Optional follow-up prompts:**
- `Produce an authorization matrix mapping endpoint, method, role/authority, and domain-level ownership requirement.`

### 9. Broken Object-Level Authorization Review

**Purpose:** Detect IDOR/BOLA vulnerabilities where users can access another user's resources.

**Context:** APIs using path, query, or body identifiers to select domain objects.

**When to use it:** For multi-user or multi-tenant applications.

**Complete prompt:**

```text
Review the current API and service layer specifically for broken object-level authorization (BOLA/IDOR).

Trace every operation that accepts or derives an object identifier such as accountId, userId, orderId, tenantId, documentId, or similar. Determine whether access is authorized against the authenticated principal and domain relationship after the object is resolved.

Look for repository lookups followed by no ownership check, client-controlled tenant identifiers, indirect access through nested resources, bulk operations, exports, update/delete endpoints, and authorization enforced only in the UI.

Classify each finding as Critical, High, Medium, or Low. Explain the exact cross-user or cross-tenant attack path before remediation.

Recommend reusable domain authorization patterns that fit the current architecture without scattering checks arbitrarily. Add tests proving one user cannot read or mutate another user's protected objects. Compile and run tests.
```

**Expected AI output:** Concrete object-level authorization vulnerabilities and maintainable domain-level fixes.

**Optional follow-up prompts:**
- `Refactor repeated ownership checks into a cohesive authorization component without weakening domain boundaries.`

---

## Spring Security

### 10. SecurityFilterChain Review

**Purpose:** Validate Spring Security 6 HTTP security configuration and rule ordering.

**Context:** Spring Boot 3.x application with one or more `SecurityFilterChain` beans.

**When to use it:** After security configuration changes or framework upgrades.

**Complete prompt:**

```text
Review every SecurityFilterChain in the current Spring Boot project.

Evaluate request matcher ordering, default authorization behavior, authentication mechanisms, exception handling, CORS, CSRF, session policy, security headers, logout, OAuth2 resource-server/client configuration, custom filters, and filter ordering.

Determine which chain matches each externally reachable route and identify gaps caused by matcher precedence or multiple chains. Look for broad permitAll rules, deprecated patterns, disabled protections without justification, and custom filters inserted at unsafe positions.

Classify findings as Critical, High, Medium, or Low and explain the runtime security effect before recommending changes.

Use Spring Security 6 idioms. Prefer framework facilities over custom filters. Make incremental changes where appropriate, include explicit Java imports, compile, and run security tests.
```

**Expected AI output:** An effective-rule analysis of all filter chains and targeted configuration fixes.

**Optional follow-up prompts:**
- `Generate MockMvc integration tests that lock down the effective matcher behavior.`

### 11. Custom Spring Security Filter Review

**Purpose:** Assess custom filters for bypasses, ordering errors, and unsafe authentication logic.

**Context:** A project containing `Filter`, `OncePerRequestFilter`, or custom security filters.

**When to use it:** Whenever custom security filters are present.

**Complete prompt:**

```text
Review all custom servlet filters that participate in authentication, authorization, token processing, tenant selection, or other security-sensitive behavior.

For each filter, explain why it exists, where it runs relative to Spring Security's built-in filters, what state it trusts or modifies, how failures are handled, and whether it can be replaced with a supported Spring Security extension point.

Look for duplicate execution, missing OncePerRequestFilter semantics, unsafe SecurityContext mutation, exception swallowing, unvalidated headers, thread-local leakage, incorrect filter ordering, authentication bypass, and behavior that differs across error or async dispatches.

Classify risks as Critical, High, Medium, or Low. Explain the exploit or failure path before fixes. Prefer removing unnecessary custom security machinery incrementally rather than rewriting unrelated code. Compile and run tests after changes.
```

**Expected AI output:** A filter-by-filter risk review with safer framework alternatives.

**Optional follow-up prompts:**
- `Replace the highest-risk custom filter with the appropriate Spring Security 6 extension point and preserve behavior with tests.`

### 12. Spring Security Upgrade Compatibility Review

**Purpose:** Find security regressions or obsolete configuration after a framework upgrade.

**Context:** A Spring Boot/Spring Security upgrade branch.

**When to use it:** During upgrades to newer Spring Boot 3.x or Spring Security 6.x releases.

**Complete prompt:**

```text
Review the current Spring Boot and Spring Security upgrade for security-semantic changes, not just compilation compatibility.

Compare the existing configuration with current Spring Security 6 behavior. Inspect matcher semantics, authorization defaults, method security, CSRF behavior, OAuth2/OIDC configuration, password encoding, session management, headers, test support, and deprecated or removed APIs.

Identify places where code compiles but the effective security behavior changed. Classify findings as Critical, High, Medium, or Low and explain the behavioral difference and security impact.

Recommend minimal modernization changes, preserve intended access rules, add regression tests for security-critical behavior, compile, and run the test suite.
```

**Expected AI output:** A semantic upgrade review focused on security regressions.

**Optional follow-up prompts:**
- `Create a before/after matrix of security behaviors affected by the upgrade.`

---

## OAuth 2.1 and OpenID Connect

### 13. OAuth 2.1 Flow Selection Review

**Purpose:** Verify that each client type uses an appropriate OAuth flow.

**Context:** Systems using authorization code, PKCE, client credentials, or legacy OAuth patterns.

**When to use it:** During identity architecture design or migration.

**Complete prompt:**

```text
Review the OAuth flows configured across the current system and assess them against modern OAuth 2.1 security practices.

Identify each client type, whether it is public or confidential, the grant or flow it uses, redirect URI behavior, PKCE usage, client authentication, token audience, scopes, refresh tokens, and browser involvement.

Flag implicit flow, resource-owner password credentials, missing PKCE for public clients, weak redirect URI validation, credentials embedded in distributable clients, excessive scopes, and misuse of access tokens as identity assertions.

Classify findings as Critical, High, Medium, or Low. Explain the attack or misuse scenario before remediation. Recommend migration paths that minimize disruption and clearly distinguish OAuth authorization from OpenID Connect authentication.
```

**Expected AI output:** A client-by-client flow assessment with migration recommendations.

**Optional follow-up prompts:**
- `Design the migration from the current legacy flow to authorization code with PKCE, including compatibility stages.`

### 14. OAuth Scope Design Review

**Purpose:** Prevent scopes from becoming an unmaintainable or overly broad authorization model.

**Context:** Resource servers and authorization servers using OAuth scopes.

**When to use it:** When adding APIs, clients, or permissions.

**Complete prompt:**

```text
Review the OAuth scope model used by the current system.

Map scopes to APIs and operations, identify who can receive each scope, and determine whether scopes represent delegated capabilities at an appropriate granularity. Detect scopes that are overly broad, ambiguous, redundant, tied to UI concepts, or incorrectly used as substitutes for domain-level authorization.

Distinguish client/application permissions from user roles and object-level permissions. Identify privilege-escalation risks created by scope combinations or default grants.

Classify findings as Critical, High, Medium, or Low. Explain tradeoffs between least privilege, token size, operational complexity, and maintainability. Recommend an incremental scope model and migration strategy.
```

**Expected AI output:** A rationalized scope model with clear separation of delegated API capability and business authorization.

**Optional follow-up prompts:**
- `Propose Spring Security authority mappings for the revised scopes without conflating them with application roles.`

### 15. OpenID Connect Login Review

**Purpose:** Validate OIDC authentication and identity claim handling.

**Context:** Spring Security OAuth2 Login with an external IdP.

**When to use it:** For browser applications using OIDC login.

**Complete prompt:**

```text
Review the current OpenID Connect login implementation.

Trace authorization request creation, state and nonce handling, redirect URIs, authorization-code exchange, ID token validation, UserInfo usage, claim mapping, account linking, session creation, logout, and refresh behavior.

Verify issuer, signature, audience, authorized-party, nonce, expiration, and other relevant token checks. Look for account takeover risks caused by trusting mutable claims, email-only account linking, missing state/nonce protection, unsafe redirect handling, or treating access-token claims as equivalent to ID-token identity.

Classify findings as Critical, High, Medium, or Low and explain attack paths before fixes. Prefer standard Spring Security OIDC facilities. Recommend incremental remediation and tests.
```

**Expected AI output:** A complete OIDC login security assessment and account-linking review.

**Optional follow-up prompts:**
- `Review only the local-user account-linking logic and propose a safer stable-identifier strategy.`

---

## JWT

### 16. JWT Validation Review

**Purpose:** Verify that resource servers reject forged, misissued, expired, or contextually invalid JWTs.

**Context:** Spring Security OAuth2 Resource Server using JWT bearer tokens.

**When to use it:** Before exposing or changing protected APIs.

**Complete prompt:**

```text
Review JWT validation in the current Spring Boot resource server.

Trace token extraction and validation. Verify signature verification, accepted algorithms, issuer, audience, expiration, not-before handling, clock skew, key selection, JWKS retrieval/caching, claim conversion, and rejection behavior.

Identify algorithm confusion, missing issuer or audience validation, permissive decoders, unsafe custom parsing, trust in unsigned claims before validation, key-rotation failures, and error responses that leak sensitive detail.

Classify findings as Critical, High, Medium, or Low. Explain how an attacker could exploit each weakness before recommending changes.

Use Spring Security 6 resource-server facilities wherever possible. Add tests for malformed, expired, wrong-issuer, wrong-audience, wrong-signature, and unsupported-algorithm tokens. Compile and run tests.
```

**Expected AI output:** Evidence-based JWT validation findings and a robust negative-test suite.

**Optional follow-up prompts:**
- `Implement explicit audience validation while preserving existing issuer validation and add regression tests.`

### 17. JWT Claims and Authority Mapping Review

**Purpose:** Prevent authorization errors caused by incorrect claims-to-authorities conversion.

**Context:** JWTs containing scopes, groups, roles, permissions, or custom claims.

**When to use it:** When integrating Cognito, Keycloak, Okta, Entra ID, or another IdP.

**Complete prompt:**

```text
Review how JWT claims are converted into Spring Security authorities in the current resource server.

Identify the claims used for scopes, groups, roles, tenant context, or permissions. Trace the JwtAuthenticationConverter or equivalent logic and determine exactly which GrantedAuthority values result.

Look for accidental ROLE_ prefix mismatches, trusting ID-token-only claims in an access-token flow, accepting client-controlled claims, mixing scopes with business roles, authority collisions, missing tenant boundaries, and privilege escalation caused by broad mappings.

Classify findings as Critical, High, Medium, or Low. Explain the effective authorization impact before proposing changes. Recommend a simple, explicit mapping model and add tests for representative tokens, including tokens that must not receive privileged authorities.
```

**Expected AI output:** A deterministic claim-to-authority model with privilege-escalation analysis.

**Optional follow-up prompts:**
- `Create a table showing source claim, example value, resulting authority, and where that authority is enforced.`

### 18. JWT Lifetime and Revocation Strategy Review

**Purpose:** Balance stateless token use with incident response and account-security requirements.

**Context:** Systems using short-lived access tokens and optional refresh tokens.

**When to use it:** When deciding token lifetimes or revocation behavior.

**Complete prompt:**

```text
Review the current JWT lifetime, refresh, logout, and revocation strategy.

Determine access-token and refresh-token lifetimes, rotation behavior, reuse detection, storage location, logout semantics, account-disable behavior, key rotation, and what happens when a token is stolen.

Identify windows in which revoked users or compromised tokens remain usable. Distinguish what can be solved by short token lifetime, refresh-token controls, authorization-server revocation, server-side deny lists, or rechecking high-risk account state.

Classify risks as Critical, High, Medium, or Low. Explain operational and scalability tradeoffs rather than recommending stateful revocation automatically.

Return a recommended strategy appropriate to the application's risk profile and concrete implementation changes only where justified.
```

**Expected AI output:** A risk-based token lifecycle strategy with explicit compromise windows and tradeoffs.

**Optional follow-up prompts:**
- `Design a step-up/revalidation approach for privileged operations without making every API request stateful.`

---

## Session Management

### 19. Web Session Security Review

**Purpose:** Identify fixation, theft, excessive lifetime, and cookie weaknesses.

**Context:** Stateful Spring Security browser application.

**When to use it:** For form login or OIDC login backed by server sessions.

**Complete prompt:**

```text
Review session management and session-cookie security in the current Spring Boot application.

Evaluate session creation policy, fixation protection, authentication-triggered session rotation, timeout, concurrent-session behavior, logout invalidation, remember-me, cookie Secure/HttpOnly/SameSite attributes, domain/path scope, reverse-proxy assumptions, and storage if sessions are distributed.

Analyze whether privileged state survives longer than intended and whether session identifiers can leak through logs, URLs, redirects, browser storage, or cross-site requests.

Classify findings as Critical, High, Medium, or Low. Explain attack scenarios before recommending fixes. Preserve user experience where possible but prioritize resistance to session theft and fixation.

Add security tests for important session lifecycle behavior and compile/run the project after changes.
```

**Expected AI output:** A session lifecycle and cookie-hardening review with validated behavior.

**Optional follow-up prompts:**
- `Recommend appropriate SameSite behavior for the application's actual cross-site authentication flow.`

---

## RBAC, ABAC, and Method Security

### 20. RBAC Model Review

**Purpose:** Detect role explosion, excessive privilege, and inconsistent role enforcement.

**Context:** Application using Spring authorities or roles.

**When to use it:** As authorization requirements grow.

**Complete prompt:**

```text
Review the current role-based access-control model.

Inventory roles, their effective permissions, where roles originate, how they map to Spring Security authorities, and where they are enforced. Identify overly broad roles, redundant roles, role explosion, implicit inheritance, hard-coded role strings, environment-specific behavior, and permissions that should be domain-scoped rather than global.

Classify security findings as Critical, High, Medium, or Low. Explain privilege-escalation and least-privilege implications before recommending changes.

Propose an incremental RBAC cleanup that preserves stable external contracts where possible. Explain when a permission, scope, ownership check, or ABAC rule would be more maintainable than another role.
```

**Expected AI output:** A role-to-permission model and prioritized least-privilege improvements.

**Optional follow-up prompts:**
- `Design a migration from coarse roles to roles plus explicit permissions without breaking existing users.`

### 21. ABAC Policy Design Review

**Purpose:** Evaluate contextual authorization rules without creating opaque policy logic.

**Context:** Authorization depends on attributes such as tenant, ownership, department, resource state, or assurance level.

**When to use it:** When RBAC alone is insufficient.

**Complete prompt:**

```text
Review the current authorization requirements and determine where attribute-based access control is appropriate.

Identify subject attributes, resource attributes, environmental attributes, and domain relationships currently used or implied by authorization decisions. Evaluate the trustworthiness and lifecycle of each attribute.

Look for authorization logic scattered across controllers, services, repositories, and UI code. Identify attributes that can be manipulated by clients or become stale.

Classify existing security gaps as Critical, High, Medium, or Low. Propose a cohesive ABAC design that integrates cleanly with Spring Security and domain services while remaining testable and understandable to maintainers.

Explain tradeoffs versus RBAC and avoid introducing a policy engine unless complexity justifies it. Provide representative allow/deny tests.
```

**Expected AI output:** A maintainable ABAC design grounded in trusted attributes and domain boundaries.

**Optional follow-up prompts:**
- `Implement one representative ABAC rule using a custom AuthorizationManager or method-security component and add tests.`

### 22. Method Security Review

**Purpose:** Verify `@PreAuthorize`, `@PostAuthorize`, and related rules protect the correct service boundaries.

**Context:** Spring Security method security enabled.

**When to use it:** When privileged business operations are protected at the service layer.

**Complete prompt:**

```text
Review method-level security across the current Spring Boot project.

Inventory @PreAuthorize, @PostAuthorize, @PreFilter, @PostFilter, secured methods, custom permission evaluators, and authorization beans referenced from SpEL. Determine whether sensitive service operations are consistently protected and whether proxying limitations can bypass enforcement through self-invocation or direct object construction.

Look for brittle string expressions, inconsistent role prefixes, unsafe parameter references, post-authorization that exposes side effects before denial, and security rules duplicated inconsistently with HTTP configuration.

Classify findings as Critical, High, Medium, or Low. Explain the actual bypass or maintenance risk before recommending changes.

Prefer readable authorization components for complex domain rules. Add focused tests for allowed and denied calls and compile/run them.
```

**Expected AI output:** A method-security coverage map and fixes for bypass or maintainability risks.

**Optional follow-up prompts:**
- `Refactor the most complex SpEL rule into a typed authorization component with explicit tests.`

---

## API and REST Security

### 23. REST API Security Review

**Purpose:** Assess security properties specific to REST endpoints.

**Context:** Spring MVC or WebFlux REST API.

**When to use it:** Before external API exposure or major API releases.

**Complete prompt:**

```text
Perform a security review of the current REST API.

Inventory endpoints and assess authentication, endpoint authorization, object-level authorization, input validation, mass assignment, content types, request-size limits, error handling, sensitive-data exposure, rate/resource abuse, idempotency where security-relevant, pagination limits, file handling, and outbound calls triggered by user input.

Identify risks before fixes and classify them as Critical, High, Medium, or Low. For each finding, explain the concrete request an attacker could make and the resulting impact.

Prefer centralized, framework-supported controls where appropriate but keep domain authorization close to domain decisions. Recommend incremental fixes and add negative integration tests for the highest-risk cases. Compile and run tests.
```

**Expected AI output:** An endpoint-focused security assessment with actionable attack scenarios.

**Optional follow-up prompts:**
- `Generate a prioritized suite of malicious-request integration tests for the API.`

### 24. Mass Assignment and Over-Posting Review

**Purpose:** Prevent clients from setting fields they should never control.

**Context:** REST controllers binding JSON directly to entities or broad DTOs.

**When to use it:** For create/update endpoints.

**Complete prompt:**

```text
Review create, update, PATCH, and bulk-modification endpoints for mass-assignment and over-posting vulnerabilities.

Trace request DTOs or entities from deserialization through mapping and persistence. Identify fields such as role, owner, tenant, status, price, approval state, audit fields, IDs, or internal flags that clients can set directly or indirectly without authorization.

Classify findings as Critical, High, Medium, or Low and explain the privilege or integrity impact before recommending fixes.

Prefer explicit request DTOs and controlled mapping over deny lists. Where applicable, use Jakarta Validation and maintain domain invariants in the service/domain layer. Make incremental changes, include explicit imports, and add tests proving protected fields cannot be manipulated.
```

**Expected AI output:** A field-level trust analysis and safe DTO/mapping changes.

**Optional follow-up prompts:**
- `Separate the current update model into user-editable and privileged administrative commands.`

### 25. API Error Handling Security Review

**Purpose:** Prevent information disclosure while retaining useful diagnostics.

**Context:** Spring exception handlers and API error responses.

**When to use it:** Before production deployment or after adding global exception handling.

**Complete prompt:**

```text
Review API exception handling and error responses for security and operability.

Inspect @ControllerAdvice, exception handlers, framework defaults, authentication/authorization failures, validation errors, persistence exceptions, and unexpected exceptions.

Identify stack traces, SQL details, internal class names, filesystem paths, token details, secrets, user enumeration signals, and inconsistent status codes that expose implementation information or aid attackers.

Classify findings as Critical, High, Medium, or Low. Explain the disclosure or enumeration risk before remediation.

Design error responses that are stable and useful to clients while preserving detailed diagnostics in protected server-side logs. Ensure correlation identifiers do not contain sensitive data. Add tests for representative failure responses.
```

**Expected AI output:** A secure error contract and separation of client-safe versus internal diagnostics.

**Optional follow-up prompts:**
- `Implement a consistent Problem Details response model using Spring Boot 3.x facilities where appropriate.`

---

## CSRF and CORS

### 26. CSRF Configuration Review

**Purpose:** Determine whether CSRF protection is required and correctly configured.

**Context:** Spring Security application using cookies, sessions, bearer tokens, or mixed authentication.

**When to use it:** Whenever CSRF is disabled or authentication mechanisms change.

**Complete prompt:**

```text
Review the current CSRF configuration based on the application's actual authentication and browser behavior.

Determine which endpoints can be invoked by browsers, which credentials browsers attach automatically, whether authentication uses cookies or bearer tokens, and whether state-changing requests are vulnerable to cross-site request forgery.

Do not assume that REST means CSRF protection is unnecessary. Identify exactly why protection is or is not required for each relevant flow.

Classify findings as Critical, High, Medium, or Low. Explain attack scenarios before recommending changes. If CSRF is disabled, verify that the architectural justification is valid. If protection is required, recommend a Spring Security 6-compatible strategy and account for SPA behavior where applicable.

Add tests demonstrating cross-site state-changing requests are rejected where protection is required.
```

**Expected AI output:** A flow-specific CSRF decision with evidence and tests.

**Optional follow-up prompts:**
- `Review whether any cookie-authenticated endpoints remain exposed after the proposed CSRF configuration.`

### 27. CORS Policy Review

**Purpose:** Prevent cross-origin policies from exposing APIs to unintended browser origins.

**Context:** APIs accessed from browser applications.

**When to use it:** When adding frontends, environments, or cross-origin integrations.

**Complete prompt:**

```text
Review the current CORS configuration in Spring Security, MVC/WebFlux, gateways, and proxies.

Determine the effective allowed origins, methods, headers, exposed headers, credential behavior, preflight handling, and environment-specific overrides. Identify wildcard origins combined with credentials, origin reflection, overly broad headers/methods, duplicated contradictory policies, and cases where CORS is incorrectly treated as an authorization control.

Classify findings as Critical, High, Medium, or Low. Explain what a malicious origin could do before recommending changes.

Recommend the narrowest maintainable policy that supports legitimate clients. Explain deployment and developer-experience tradeoffs. Add tests for allowed and disallowed origins where practical.
```

**Expected AI output:** An effective CORS policy analysis and least-permissive configuration.

**Optional follow-up prompts:**
- `Consolidate duplicated CORS configuration into one authoritative layer without changing valid clients.`

---

## Password Storage and Credential Handling

### 28. Password Storage Review

**Purpose:** Verify passwords are hashed with an appropriate adaptive algorithm and lifecycle.

**Context:** Application stores or validates local passwords.

**When to use it:** For legacy authentication or local identity stores.

**Complete prompt:**

```text
Review password storage and verification in the current application.

Identify password encoders, hash formats, cost parameters, salts, migration behavior, password-reset handling, temporary passwords, logging, DTO exposure, and database schema constraints.

Detect plaintext storage, reversible encryption, fast hashes, hard-coded salts, weak legacy encoders, unsafe fallback behavior, password leakage, and migrations that force insecure downgrade paths.

Classify findings as Critical, High, Medium, or Low. Explain credential-compromise impact before fixes.

Prefer Spring Security PasswordEncoder facilities and an adaptive algorithm appropriate to the deployment environment. If legacy hashes exist, propose a safe on-login migration strategy rather than a disruptive bulk reset when feasible. Add tests without embedding real credentials.
```

**Expected AI output:** A password-hash and migration assessment with practical cost-setting rationale.

**Optional follow-up prompts:**
- `Design a DelegatingPasswordEncoder migration path for the current legacy hash format.`

---

## Secrets and Key Management

### 29. Application Secrets Review

**Purpose:** Find hard-coded, leaked, overexposed, or poorly rotated secrets.

**Context:** Repository, configuration, CI/CD, Kubernetes, and cloud environment.

**When to use it:** Before production deployment or after credential incidents.

**Complete prompt:**

```text
Review the current project for secret-management risks.

Inspect application configuration, source code, test resources, Gradle files, Dockerfiles, Kubernetes manifests, CI/CD definitions, scripts, documentation, and environment-variable usage. Identify API keys, passwords, client secrets, private keys, tokens, connection strings, and credentials that are hard-coded, committed, logged, broadly exposed, or difficult to rotate.

Do not print discovered secret values in the report. Redact sensitive material and reference locations safely.

Classify findings as Critical, High, Medium, or Low. Explain exposure and blast radius before recommending remediation.

Recommend an incremental secret-management model appropriate to the existing platform, including access control, rotation, local development, testing, and incident response. If a live secret appears committed, treat rotation as distinct from merely deleting it from the latest revision.
```

**Expected AI output:** A redacted secret inventory, exposure analysis, and rotation-aware remediation plan.

**Optional follow-up prompts:**
- `Refactor application configuration to consume the platform's secret store without introducing secrets into source-controlled files.`

### 30. Cryptographic Key Management Review

**Purpose:** Assess lifecycle, storage, access, and rotation of encryption/signing keys.

**Context:** Applications performing encryption, signing, token issuance, or certificate management.

**When to use it:** When cryptographic keys are application-managed or cloud-KMS-backed.

**Complete prompt:**

```text
Review cryptographic key management across the current system.

Inventory signing keys, encryption keys, private certificates, data-encryption keys, key-encryption keys, and any application-managed cryptographic material. Determine where keys are generated, stored, loaded, cached, authorized, rotated, versioned, backed up, and retired.

Identify keys stored in source control, container images, ordinary configuration, database rows without protection, or long-lived process memory unnecessarily. Evaluate separation of duties and whether services have broader decrypt/sign permissions than required.

Classify findings as Critical, High, Medium, or Low. Explain compromise impact and rotation difficulty before remediation.

Prefer managed KMS/HSM capabilities where they materially reduce risk. Explain availability, latency, cost, and operational tradeoffs. Return a target key lifecycle and incremental migration plan.
```

**Expected AI output:** A key lifecycle model and least-privilege key-access plan.

**Optional follow-up prompts:**
- `Design envelope encryption for the current sensitive-data use case using the existing cloud platform.`

---

## Encryption, HTTPS, and Certificates

### 31. Application Cryptography Review

**Purpose:** Detect unsafe algorithms, modes, IV/nonce reuse, and homegrown crypto.

**Context:** Java code performing encryption, hashing, MACs, or signatures.

**When to use it:** Whenever application code directly uses JCA/JCE or cryptographic libraries.

**Complete prompt:**

```text
Review all application-level cryptographic code in the current Java project.

Identify algorithms, modes, padding, key sizes, nonce/IV generation, random-number sources, key derivation, encoding, signature verification, MAC usage, and error handling. Determine whether cryptography is being used for confidentiality, integrity, authentication, or password storage and whether the primitive matches the goal.

Flag ECB, static or reused IVs/nonces, unauthenticated encryption, weak hashes, insecure randomness, custom cryptographic protocols, hard-coded keys, ambiguous encodings, and verification logic vulnerable to bypass.

Classify findings as Critical, High, Medium, or Low. Explain the cryptographic failure mode before recommending changes.

Prefer established high-level constructions and platform-managed encryption where appropriate. Avoid unnecessary cryptography. If changing code, preserve data migration requirements, use explicit imports, compile, and add deterministic tests without weakening randomness in production code.
```

**Expected AI output:** A cryptographic misuse assessment and migration-safe remediation plan.

**Optional follow-up prompts:**
- `Design a backward-compatible migration from the current encrypted data format to authenticated encryption.`

### 32. TLS and HTTPS Configuration Review

**Purpose:** Verify secure transport from client to application and between internal services.

**Context:** Spring Boot behind proxies/load balancers or using direct TLS.

**When to use it:** Before production or after networking changes.

**Complete prompt:**

```text
Review HTTPS/TLS configuration across the current deployment path.

Trace TLS from external clients through CDN, load balancer, ingress, gateway, service mesh, application, and outbound service calls as applicable. Identify termination points, re-encryption, certificate validation, hostname verification, protocol versions, cipher policy, forwarded-header handling, redirect behavior, HSTS, and plaintext internal hops.

Look for trust-all clients, disabled hostname verification, insecure protocols, mixed-content assumptions, spoofable X-Forwarded-* handling, and services that incorrectly infer security from untrusted proxy headers.

Classify findings as Critical, High, Medium, or Low. Explain attack paths before fixes. Recommend controls appropriate to the actual threat model rather than demanding mTLS everywhere.
```

**Expected AI output:** An end-to-end transport security map and targeted TLS hardening recommendations.

**Optional follow-up prompts:**
- `Focus on outbound Java HTTP clients and verify certificate and hostname validation cannot be bypassed.`

### 33. Certificate Lifecycle Review

**Purpose:** Prevent outages and compromise caused by unmanaged certificates.

**Context:** TLS, mTLS, signing certificates, or private PKI.

**When to use it:** For production-readiness or certificate incidents.

**Complete prompt:**

```text
Review certificate management in the current system.

Inventory certificates and trust stores used by applications, ingress, load balancers, service-to-service mTLS, outbound integrations, and signing workflows. Determine issuer, ownership, renewal mechanism, expiration monitoring, private-key storage, trust-anchor distribution, and rotation procedure.

Identify manually renewed certificates, embedded trust stores, excessive certificate lifetimes, shared private keys, missing expiration alerts, brittle pinning, and trust stores that accept more issuers than required.

Classify findings as Critical, High, Medium, or Low. Explain compromise or availability impact before recommending changes.

Return a certificate lifecycle model, automation opportunities, monitoring requirements, and safe rotation plan.
```

**Expected AI output:** A certificate inventory and operationally safe lifecycle plan.

**Optional follow-up prompts:**
- `Design a zero-downtime certificate rotation procedure for the current deployment.`

---

## Identity Providers

### 34. Identity Provider Integration Review

**Purpose:** Assess application assumptions about an external IdP.

**Context:** OIDC/OAuth integration with Cognito, Keycloak, Okta, Entra ID, or similar.

**When to use it:** During IdP onboarding, migration, or security review.

**Complete prompt:**

```text
Review the application's integration with its configured Identity Provider.

Inspect discovery/metadata configuration, issuer validation, client registration, redirect URIs, secrets or private-key authentication, scopes, claims, group/role mapping, token lifetimes, logout, refresh, account lifecycle, and environment separation.

Identify application logic that depends on unstable or provider-specific claims without an explicit abstraction. Look for insecure defaults, wildcard redirects, excessive scopes, weak client authentication, and role mappings that grant unintended privilege.

Classify findings as Critical, High, Medium, or Low. Explain risks before remediation and distinguish IdP configuration changes from application changes.

Recommend incremental improvements and tests that protect the application's security contract even if provider configuration changes.
```

**Expected AI output:** A provider/application boundary assessment with claim and lifecycle risks.

**Optional follow-up prompts:**
- `Define a provider-neutral internal identity model for the claims the application actually needs.`

### 35. AWS Cognito Security Review

**Purpose:** Review Cognito-specific authentication and authorization configuration.

**Context:** Spring Boot application integrated with Amazon Cognito user pools.

**When to use it:** For Cognito-backed applications and resource servers.

**Complete prompt:**

```text
Review the current Amazon Cognito integration from both application and identity-configuration perspectives using available authorized read-only context.

Evaluate user pool and app client assumptions, OAuth flows, PKCE, callback/logout URLs, client secrets, hosted UI or managed login behavior, MFA policy, password policy, token lifetimes, scopes, resource servers, groups, access-token versus ID-token claims, issuer/audience validation, and Spring Security authority mapping.

Pay particular attention to attempts to authorize APIs using claims that exist only in ID tokens, unsafe cognito:groups mappings, broad app-client permissions, and incorrect treatment of Cognito authentication as AWS IAM authorization.

Classify findings as Critical, High, Medium, or Low. Explain risks before recommending fixes. Separate Cognito configuration, Spring Security configuration, and application-domain authorization recommendations.
```

**Expected AI output:** A Cognito-specific review that clearly separates ID tokens, access tokens, groups, scopes, and IAM.

**Optional follow-up prompts:**
- `Design a clean Cognito-to-Spring authority mapping for the current groups and scopes.`

### 36. Spring Authorization Server Review

**Purpose:** Assess a self-hosted OAuth/OIDC authorization server built with Spring.

**Context:** Spring Authorization Server deployment.

**When to use it:** Before production rollout or protocol/configuration changes.

**Complete prompt:**

```text
Review the current Spring Authorization Server implementation as a security-critical identity component.

Evaluate registered clients, client authentication methods, redirect URIs, grant types, PKCE requirements, consent, scopes, token formats, token lifetimes, refresh-token rotation, signing keys, JWK publication, OIDC configuration, issuer settings, persistence, logout, and administrative client-registration paths.

Identify unsafe client defaults, wildcard redirects, weak secrets, public clients treated as confidential, missing PKCE, excessive token lifetime, insecure key storage, and extension code that weakens protocol guarantees.

Classify findings as Critical, High, Medium, or Low. Explain protocol-level attack paths before remediation. Prefer standards-compliant framework extension points and incremental changes. Add protocol/security tests and compile/run them.
```

**Expected AI output:** A production-readiness assessment for the authorization server itself.

**Optional follow-up prompts:**
- `Review registered clients and propose a least-privilege client policy by client type.`

### 37. Keycloak Integration Review

**Purpose:** Validate Spring application integration with Keycloak without relying on obsolete adapters.

**Context:** Spring Boot 3.x / Spring Security 6 with Keycloak OIDC/OAuth.

**When to use it:** For modernizing or reviewing Keycloak integration.

**Complete prompt:**

```text
Review the current Keycloak integration using modern Spring Security 6 OAuth2/OIDC support.

Inspect issuer configuration, client type, redirect URIs, PKCE, client credentials, realm/client roles, groups, scopes, protocol mappers, access-token claims, ID-token claims, logout, and Spring authority conversion.

Identify obsolete Keycloak adapter patterns, role-mapping ambiguity, overly broad protocol mappers, tokens containing unnecessary sensitive claims, and application authorization that depends directly on Keycloak internals.

Classify findings as Critical, High, Medium, or Low. Explain the effective privilege or identity risk before recommending changes. Prefer provider-neutral Spring Security interfaces where practical.
```

**Expected AI output:** A modern Keycloak integration review with clean role/claim mapping guidance.

**Optional follow-up prompts:**
- `Refactor provider-specific authorization code behind a small identity/authority mapping boundary.`

### 38. Microsoft Entra ID Integration Review

**Purpose:** Review enterprise OIDC/OAuth integration with Microsoft Entra ID.

**Context:** Spring Boot application using Entra ID tokens.

**When to use it:** During enterprise SSO/API integration.

**Complete prompt:**

```text
Review the current Microsoft Entra ID integration.

Evaluate tenant/issuer configuration, single-tenant versus multi-tenant assumptions, application registrations, redirect URIs, client authentication, delegated scopes, application permissions, groups/app roles, token audiences, claims mapping, and Spring Security resource-server or OIDC-login configuration.

Identify tenant-confusion risks, incorrect issuer validation, acceptance of tokens for another audience, overprivileged application permissions, group overage assumptions, and confusion between delegated user permissions and application permissions.

Classify findings as Critical, High, Medium, or Low. Explain attack paths before remediation. Recommend incremental application and identity-configuration changes with least privilege.
```

**Expected AI output:** A tenant-aware Entra ID integration assessment.

**Optional follow-up prompts:**
- `Focus on multi-tenant token validation and identify every assumption that could allow cross-tenant access.`

### 39. Okta Integration Review

**Purpose:** Validate Okta OIDC/OAuth configuration and application mappings.

**Context:** Spring Boot application integrated with Okta.

**When to use it:** During SSO/resource-server security review.

**Complete prompt:**

```text
Review the current Okta integration from the Spring Security and identity-policy perspectives.

Inspect authorization server/issuer selection, client configuration, redirect URIs, PKCE, client authentication, scopes, groups/claims, access policies, token lifetime, audience validation, logout, and authority mapping.

Identify use of the wrong authorization server, broad group claims, insecure redirects, excessive scopes, weak client-secret handling, and application authorization that trusts claims not intended for the resource server.

Classify findings as Critical, High, Medium, or Low. Explain impact before fixes. Recommend a minimal, provider-aware but maintainable configuration and appropriate tests.
```

**Expected AI output:** An Okta-specific configuration and claims review.

**Optional follow-up prompts:**
- `Create negative JWT tests for tokens from the wrong Okta issuer or audience.`

---

## AWS IAM and Least Privilege

### 40. AWS IAM Integration Review

**Purpose:** Review how the application obtains and uses AWS permissions.

**Context:** Spring Boot workloads on AWS using SDKs, EKS, ECS, EC2, Lambda, or local development credentials.

**When to use it:** Before production deployment or when adding AWS service access.

**Complete prompt:**

```text
Review how the current application authenticates to AWS and receives IAM permissions, using authorized read-only AWS context where available.

Trace credential acquisition for each runtime environment. Evaluate IAM roles, workload identity such as EKS IRSA/Pod Identity where applicable, instance/task roles, local credential behavior, STS usage, trust policies, resource policies, and AWS SDK credential-provider configuration.

Identify static AWS access keys, credential fallback surprises, overly broad trust relationships, cross-account risks, wildcard actions/resources, and application code that assumes end-user OAuth identity is equivalent to AWS IAM identity.

Classify findings as Critical, High, Medium, or Low. Explain credential-theft and privilege-escalation paths before recommending changes.

Prefer short-lived workload credentials and least privilege. Return an incremental migration plan and clearly separate application identity, workload identity, and end-user identity.
```

**Expected AI output:** A workload-identity and IAM permission assessment with clear identity separation.

**Optional follow-up prompts:**
- `Design the least-privilege IAM role required by the current service based on its actual AWS API usage.`

### 41. IAM Least-Privilege Policy Review

**Purpose:** Reduce AWS permissions to the actions and resources actually required.

**Context:** IAM policies associated with application workloads.

**When to use it:** After feature growth or before production approval.

**Complete prompt:**

```text
Review the IAM permissions used by the current workload using the repository and authorized read-only AWS configuration.

Map actual AWS SDK/CLI/service usage to required IAM actions and resources. Inspect identity policies, resource policies, permission boundaries, role trust policies, and relevant conditions.

Identify wildcard actions, wildcard resources, broad service permissions, unnecessary write/delete privileges, missing resource or tag conditions, pass-role exposure, KMS overreach, secrets access beyond need, and privilege-escalation combinations.

Classify findings as Critical, High, Medium, or Low. Explain the abuse path before recommending policy changes.

Propose the smallest maintainable policy that supports required runtime behavior. Avoid brittle overfitting where AWS APIs legitimately require broader resource syntax. Explain those exceptions.
```

**Expected AI output:** A usage-derived least-privilege IAM policy design with escalation analysis.

**Optional follow-up prompts:**
- `Generate a staged policy-tightening plan that can be deployed safely without causing an avoidable outage.`

---

## Dependency, Supply Chain, and SBOM Security

### 42. Gradle Dependency Vulnerability Review

**Purpose:** Assess vulnerable dependencies in context rather than merely listing CVEs.

**Context:** Java 21 / Spring Boot 3.x Gradle project.

**When to use it:** During routine maintenance, release review, or vulnerability response.

**Complete prompt:**

```text
Review the current Gradle dependency graph for security vulnerabilities and risky dependency choices.

Inspect direct and transitive dependencies, Spring Boot dependency management, plugins, repositories, version constraints, dependency locking or verification, and known vulnerability data available to the environment.

For each relevant vulnerability, determine whether the vulnerable component and code path are actually present and reachable in this application. Do not equate a scanner match with exploitability.

Classify findings as Critical, High, Medium, or Low based on vulnerability severity, reachability, exposure, and compensating controls. Explain risk before recommending upgrades.

Prefer supported patch/minor upgrades and Spring Boot-managed versions over ad hoc overrides. Identify compatibility risks, make incremental changes where safe, run Gradle tests, and report residual vulnerable transitive dependencies.
```

**Expected AI output:** A reachability-aware dependency risk report and safe upgrade plan.

**Optional follow-up prompts:**
- `Upgrade only the High and Critical dependency findings with the smallest compatible version changes and run the full test suite.`

### 43. Gradle Build Supply Chain Review

**Purpose:** Protect dependency and plugin resolution from tampering.

**Context:** Gradle build files, settings, wrapper, repositories, and CI.

**When to use it:** For supply-chain hardening.

**Complete prompt:**

```text
Review the current Gradle build for software supply-chain security.

Inspect the Gradle wrapper, distribution URL, wrapper checksum configuration, plugin repositories, dependency repositories, repository ordering, dynamic versions, snapshots, dependency locking, dependency verification, custom build scripts, init scripts, and credentials used for private artifact repositories.

Identify mutable or untrusted artifact sources, HTTP repositories, broad repository fallbacks, dependency confusion opportunities, unpinned build tooling, credentials exposed to builds, and scripts that download or execute remote content without integrity verification.

Classify findings as Critical, High, Medium, or Low. Explain compromise paths before recommending changes.

Recommend incremental hardening that preserves reproducible builds and developer usability. Run Gradle verification/tests after build changes.
```

**Expected AI output:** A build-chain threat assessment and reproducibility/integrity improvements.

**Optional follow-up prompts:**
- `Add Gradle dependency verification and explain how the team should maintain the verification metadata.`

### 44. SBOM Generation and Operationalization Review

**Purpose:** Make SBOMs useful for vulnerability response rather than compliance-only artifacts.

**Context:** Gradle-built services and container images.

**When to use it:** For release pipelines and supply-chain governance.

**Complete prompt:**

```text
Review the current build and release process and design an SBOM strategy for the application and its deployable artifacts.

Determine which dependencies and components must be represented, including Java dependencies, container base images, OS packages where applicable, and generated artifacts. Prefer a standard format such as CycloneDX or SPDX that integrates with the existing toolchain.

Assess where SBOM generation should occur, how it should be tied to immutable build artifacts, how integrity/provenance should be preserved, where SBOMs should be stored, and how they will be queried during vulnerability response.

Identify current gaps and classify material supply-chain risks as Critical, High, Medium, or Low.

Implement the smallest maintainable Gradle/pipeline integration if appropriate, without making the build dependent on unnecessary external services. Validate generation and describe how to verify the SBOM corresponds to the released artifact.
```

**Expected AI output:** A practical SBOM architecture plus build integration and verification strategy.

**Optional follow-up prompts:**
- `Add CycloneDX SBOM generation to the Gradle build and verify the output against the resolved dependency graph.`

---

## OWASP and Static Security Review

### 45. OWASP Top 10 Implementation Review

**Purpose:** Use OWASP categories as structured coverage while remaining implementation-specific.

**Context:** Production Spring Boot application.

**When to use it:** For broad application security reviews.

**Complete prompt:**

```text
Review the current application against the current OWASP Top 10 as a coverage framework.

For each category, inspect the actual code and configuration for relevant attack surfaces. Do not manufacture findings simply to populate every category. Focus on concrete evidence involving access control, cryptography, injection, insecure design, misconfiguration, vulnerable components, authentication failures, software/data integrity, logging/monitoring, and server-side request behavior as applicable.

For every material finding, provide evidence, realistic attack path, affected assets, existing controls, and severity: Critical, High, Medium, or Low.

Explain risks before fixes. Recommend incremental production-quality remediation and security tests for High and Critical issues. Identify categories reviewed with no material finding and state what was examined.
```

**Expected AI output:** An OWASP-organized but evidence-driven security assessment.

**Optional follow-up prompts:**
- `Convert the High and Critical findings into code-review tasks with acceptance criteria and regression tests.`

### 46. Static Secure Code Review

**Purpose:** Perform a senior-level manual security review of Java code patterns.

**Context:** Java 21 / Spring Boot repository.

**When to use it:** Before releases or after significant code changes.

**Complete prompt:**

```text
Perform a static secure-code review of the current Java 21 / Spring Boot repository.

Prioritize code paths that cross trust boundaries or handle authentication, authorization, persistence, files, XML, serialization, templates, outbound HTTP, process execution, cryptography, secrets, and privileged administration.

Look for exploitable data flows rather than isolated suspicious APIs. Trace untrusted input to sensitive sinks and evaluate validation, canonicalization, encoding, authorization, and framework protections along the path.

Classify findings as Critical, High, Medium, or Low. For each finding include source location, data flow, exploit prerequisites, impact, and why existing controls are insufficient.

Recommend incremental fixes and tests. Avoid speculative warnings unsupported by the implementation. If fixes are made, include explicit imports, compile, and run relevant tests.
```

**Expected AI output:** A source-linked, data-flow-oriented secure code review.

**Optional follow-up prompts:**
- `Fix only findings with a demonstrated attacker-controlled source-to-sensitive-sink path and add regression tests.`

### 47. Security-Focused Pull Request Review

**Purpose:** Review a change set for newly introduced security risk.

**Context:** Current branch or pull request compared with its base.

**When to use it:** For security-sensitive code review.

**Complete prompt:**

```text
Review the current change set as a security-focused pull request reviewer.

Analyze the diff in the context of the surrounding code. Identify changes to trust boundaries, authentication, authorization, data validation, persistence, logging, secrets, dependencies, configuration, network exposure, file handling, serialization, cryptography, and deployment privileges.

Prioritize regressions introduced by the change rather than pre-existing unrelated issues, but note a pre-existing issue if the new change materially increases its exploitability.

Classify findings as Critical, High, Medium, or Low. For each one explain the exploit path, why the change introduces or exposes it, and the smallest safe remediation.

Check whether tests cover denied/invalid/adversarial behavior, not only happy paths. Return blocking findings separately from non-blocking hardening suggestions.
```

**Expected AI output:** A diff-aware security review suitable for a real pull request.

**Optional follow-up prompts:**
- `Implement the blocking security fixes without refactoring unrelated code, then run the affected tests.`

---

## Security and Audit Logging

### 48. Security Logging Review

**Purpose:** Ensure attacks and authentication failures are observable without leaking secrets.

**Context:** Application logs and security event handling.

**When to use it:** Before production or during observability improvements.

**Complete prompt:**

```text
Review security-relevant logging in the current application.

Determine whether authentication failures, authorization denials, suspicious validation failures, administrative actions, token-validation failures, account changes, and security-control failures are logged at useful levels with appropriate context.

Identify missing events as well as dangerous logging of passwords, tokens, session IDs, API keys, authorization headers, personal data, secrets, cryptographic material, or full request bodies.

Classify material risks as Critical, High, Medium, or Low. Explain incident-detection or disclosure impact before recommending changes.

Recommend structured logging fields, correlation strategy, redaction, rate controls, and log-level choices. Preserve enough information for incident investigation without creating a secondary sensitive-data store.
```

**Expected AI output:** A balanced detection-versus-disclosure logging strategy.

**Optional follow-up prompts:**
- `Implement centralized redaction for the highest-risk sensitive fields and add tests proving they do not appear in logs.`

### 49. Audit Logging Design Review

**Purpose:** Ensure business-critical actions produce tamper-resistant, attributable audit records.

**Context:** Applications with administrative, financial, compliance, or sensitive-data operations.

**When to use it:** When ordinary application logs are insufficient for accountability.

**Complete prompt:**

```text
Review the current system's audit requirements and implementation.

Identify actions that require durable audit records, such as privilege changes, administrative operations, sensitive-data access, approvals, security-setting changes, account lifecycle events, and high-value business transactions.

For each event, determine actor identity, effective authority, target resource, action, outcome, timestamp, request/correlation context, and relevant before/after state without recording secrets unnecessarily.

Assess tamper resistance, write authorization, retention, ordering, clock assumptions, failure behavior, and access to audit data.

Classify security/compliance gaps as Critical, High, Medium, or Low. Explain why ordinary debug/application logs are insufficient where applicable. Recommend an incremental audit architecture and explicit failure policy for security-critical events.
```

**Expected AI output:** An audit event model and trustworthy storage/access design.

**Optional follow-up prompts:**
- `Design the audit schema and Java service boundary for the three highest-value administrative actions.`

---

## Input Validation and Injection

### 50. Input Validation Boundary Review

**Purpose:** Ensure untrusted data is validated at appropriate boundaries without duplicating domain rules everywhere.

**Context:** Spring MVC/WebFlux controllers, messaging consumers, batch inputs, and integrations.

**When to use it:** For broad input-hardening reviews.

**Complete prompt:**

```text
Review input validation across every external trust boundary in the current application.

Inspect REST requests, headers, path/query parameters, file inputs, message consumers, scheduled/batch imports, webhook payloads, and third-party responses where relevant. Distinguish syntactic validation from domain invariants and authorization.

Identify missing size/range/format constraints, dangerous normalization assumptions, validation performed after sensitive processing, inconsistent validation across alternate entry points, and custom validators that fail open.

Classify findings as Critical, High, Medium, or Low. Explain the downstream security impact before fixes.

Use Jakarta Validation where appropriate, but keep business invariants in the domain/service layer. Recommend reusable validation boundaries and add adversarial tests for high-risk inputs.
```

**Expected AI output:** A trust-boundary validation map with appropriate placement of structural and domain validation.

**Optional follow-up prompts:**
- `Implement size and format constraints for externally supplied identifiers and payload fields that can cause resource abuse.`

### 51. SQL Injection Review

**Purpose:** Find attacker-controlled SQL construction and unsafe native-query patterns.

**Context:** Jakarta Persistence, Spring Data JPA, JDBC, or native SQL.

**When to use it:** For persistence security reviews or legacy SQL code.

**Complete prompt:**

```text
Review the current persistence layer for SQL injection risks.

Trace attacker-controlled values into JPQL, HQL, native queries, JdbcTemplate calls, stored-procedure calls, dynamic ORDER BY clauses, search filters, and query-building utilities.

Distinguish safe parameter binding from string concatenation and identify cases where identifiers or sort expressions cannot be parameterized and therefore require strict allow-listing.

Classify findings as Critical, High, Medium, or Low. Explain the exact input-to-query path and likely database impact before remediation.

Prefer parameterized queries, Criteria/specification APIs where they improve safety and maintainability, and allow-listed mappings for dynamic identifiers. Do not rewrite safe JPA repository methods unnecessarily. Add regression tests for demonstrated vulnerable paths.
```

**Expected AI output:** Source-to-SQL data-flow findings with precise fixes.

**Optional follow-up prompts:**
- `Replace the vulnerable dynamic query construction with a maintainable allow-listed query strategy and run persistence tests.`

### 52. XSS and Output Encoding Review

**Purpose:** Detect stored, reflected, and DOM-relevant XSS risks in server-rendered or API-fed web applications.

**Context:** Spring MVC templates or APIs serving browser-consumed content.

**When to use it:** When untrusted text reaches HTML or script contexts.

**Complete prompt:**

```text
Review the current application for cross-site scripting risks.

Trace untrusted data into HTML templates, attributes, JavaScript contexts, URLs, rich-text rendering, error pages, administrative views, and API fields that are later rendered by known frontend code in the workspace.

Determine whether the rendering technology performs context-appropriate escaping by default and identify explicit unescaped/raw rendering, unsafe HTML sanitization, string-built markup, and stored content that crosses privilege boundaries.

Classify findings as Critical, High, Medium, or Low. Explain the execution context and victim impact before remediation.

Prefer context-aware output encoding and well-maintained sanitization only when HTML input is intentionally supported. Avoid destructive input encoding at storage time. Add tests for high-risk rendering paths.
```

**Expected AI output:** Context-specific XSS findings that distinguish encoding from sanitization.

**Optional follow-up prompts:**
- `Harden the rich-text path while preserving the explicitly supported formatting subset.`

### 53. XXE and XML Parser Review

**Purpose:** Prevent external entity expansion, local file disclosure, SSRF, and parser abuse.

**Context:** Java code parsing XML, SOAP, SAML-related documents, feeds, or uploaded XML.

**When to use it:** Whenever untrusted XML is processed.

**Complete prompt:**

```text
Review all XML parsing and transformation code for XXE and related parser risks.

Identify DocumentBuilderFactory, SAXParserFactory, XMLInputFactory, TransformerFactory, SchemaFactory, JAXB/unmarshaller configuration, third-party XML parsers, and framework defaults used on untrusted or semi-trusted XML.

Determine whether DTDs, external general entities, external parameter entities, external schemas, and network/file resolution are disabled unless explicitly required. Evaluate entity expansion and resource-exhaustion protections.

Classify findings as Critical, High, Medium, or Low. Explain whether exploitation enables file disclosure, SSRF, denial of service, or another impact before remediation.

Apply parser-specific hardening rather than generic flags that do not affect the actual parser. Add malicious XML tests and compile/run them.
```

**Expected AI output:** Parser-specific XXE findings and verified secure configuration.

**Optional follow-up prompts:**
- `Create a regression test using an external-entity payload that must fail without performing network or file access.`

### 54. SSRF Review

**Purpose:** Detect attacker influence over server-side outbound requests.

**Context:** HTTP clients, webhooks, URL importers, callbacks, image fetchers, or metadata retrieval.

**When to use it:** Whenever user-controlled data can affect a destination URL or host.

**Complete prompt:**

```text
Review all server-side outbound network requests for SSRF risk.

Trace untrusted input into URI/URL construction, redirects, DNS resolution, proxy configuration, webhook callbacks, file importers, image/document fetchers, and cloud metadata access paths.

Evaluate scheme restrictions, hostname allow-lists, IP-range blocking, redirect revalidation, DNS rebinding exposure, alternate IP representations, credential forwarding, response-size limits, and timeout behavior.

Classify findings as Critical, High, Medium, or Low. Explain reachable internal/cloud targets and credential exposure before fixes.

Recommend controls appropriate to the business requirement. Prefer positive destination allow-lists where destinations are known. Add adversarial tests that do not contact real sensitive endpoints.
```

**Expected AI output:** A destination-control analysis covering redirects, DNS, and cloud metadata risks.

**Optional follow-up prompts:**
- `Implement a reusable outbound-destination validator for the current fixed-partner integration model.`

### 55. Path Traversal Review

**Purpose:** Prevent filesystem access outside intended directories.

**Context:** File download, export, import, template, archive, or storage code.

**When to use it:** When paths are influenced by external input.

**Complete prompt:**

```text
Review filesystem and object-key construction for path traversal and unsafe file access.

Trace user-controlled filenames, IDs, archive entries, relative paths, and storage keys through normalization and file operations. Look for ../ traversal, absolute paths, encoded separators, symlink escapes, archive extraction traversal, platform-specific path behavior, and checks performed before canonicalization.

Classify findings as Critical, High, Medium, or Low. Explain what files could be read, overwritten, or created before remediation.

Prefer server-generated identifiers and resolved-path containment checks rather than string replacement. Add tests for traversal variants and preserve legitimate filenames where possible.
```

**Expected AI output:** A canonical-path and containment review with platform-aware tests.

**Optional follow-up prompts:**
- `Harden archive extraction against Zip Slip and add regression tests for malicious entry names.`

### 56. Deserialization Security Review

**Purpose:** Detect unsafe object deserialization and polymorphic binding.

**Context:** Java serialization, Jackson, YAML, XML, caches, messaging, or stored serialized objects.

**When to use it:** When external or persisted data is converted into object graphs.

**Complete prompt:**

```text
Review the current application for unsafe deserialization.

Identify Java native serialization, ObjectInputStream, Jackson polymorphic typing, YAML object construction, XML object binding, session/cache serialization, message payload deserialization, and custom type-resolution mechanisms.

Determine whether attacker-controlled data can select arbitrary classes or trigger constructors/setters/gadget behavior. Evaluate allow-lists, type metadata, trusted boundaries, and library versions.

Classify findings as Critical, High, Medium, or Low. Explain potential code execution, privilege manipulation, or denial-of-service impact before fixes.

Prefer simple explicit DTO formats and safe type allow-lists. Avoid broad default typing. Recommend migration strategies where serialized persisted data creates compatibility constraints. Add malicious payload tests.
```

**Expected AI output:** A serialization trust-boundary review with migration-aware fixes.

**Optional follow-up prompts:**
- `Replace the unsafe polymorphic payload with an explicit discriminator-to-type mapping and preserve wire compatibility if possible.`

---

## File Upload Security

### 57. File Upload Security Review

**Purpose:** Prevent malicious files, traversal, resource abuse, and unsafe downstream processing.

**Context:** Multipart upload or object-storage ingestion.

**When to use it:** For any user- or partner-supplied files.

**Complete prompt:**

```text
Review the complete file-upload lifecycle in the current application.

Trace files from request acceptance through validation, temporary storage, naming, scanning, parsing, object storage, metadata persistence, download, and deletion. Evaluate authentication, authorization, size limits, count limits, filename handling, MIME/type detection, extension checks, content validation, archive handling, malware scanning where justified, storage isolation, executable permissions, and public accessibility.

Identify attacks involving path traversal, content-type spoofing, polyglot files, decompression bombs, parser exploits, overwrite, stored XSS, unauthorized download, and resource exhaustion.

Classify findings as Critical, High, Medium, or Low. Explain attack paths before fixes. Recommend layered controls proportional to how files are subsequently used. Add adversarial tests for the highest-risk paths.
```

**Expected AI output:** An end-to-end upload threat analysis rather than a MIME-extension checklist.

**Optional follow-up prompts:**
- `Design a quarantine-and-promote workflow for files that require asynchronous malware scanning.`

---

## Container and Docker Security

### 58. Dockerfile Security Review

**Purpose:** Harden application container images without sacrificing maintainability.

**Context:** Dockerfiles building Java/Spring Boot services.

**When to use it:** Before production deployment or base-image changes.

**Complete prompt:**

```text
Review the current Dockerfile and container build for security.

Evaluate base-image provenance and support status, image pinning strategy, multi-stage builds, package installation, remote downloads, build secrets, copied files, ownership/permissions, runtime user, writable filesystem needs, exposed ports, health checks where relevant, JVM/container settings, and unnecessary shells/tools in the final image.

Identify secrets baked into layers, root execution, excessive packages, unverified downloads, mutable tags, unsafe ADD usage, broad file permissions, and build context leakage.

Classify findings as Critical, High, Medium, or Low. Explain compromise or supply-chain impact before fixes.

Recommend incremental Dockerfile changes. Preserve reproducible Gradle builds and application behavior. Build the image and run relevant tests or smoke checks when the environment supports it.
```

**Expected AI output:** A production-focused Docker hardening review with supply-chain considerations.

**Optional follow-up prompts:**
- `Produce a hardened multi-stage Dockerfile for the current Gradle application while preserving existing runtime requirements.`

### 59. Container Runtime Security Review

**Purpose:** Assess privileges granted to a running container.

**Context:** Kubernetes/ECS/container runtime configuration.

**When to use it:** Before production or when adding host integrations.

**Complete prompt:**

```text
Review the runtime security configuration for the current application container.

Evaluate user/group IDs, Linux capabilities, privileged mode, allowPrivilegeEscalation, read-only root filesystem, seccomp, AppArmor/SELinux where applicable, mounted host paths, device access, writable volumes, environment secrets, process namespace sharing, and resource limits.

Determine which privileges the Java application actually requires. Identify runtime permissions that could turn an application compromise into node or host compromise.

Classify findings as Critical, High, Medium, or Low. Explain the escalation path before recommending changes.

Prefer least-privilege runtime settings and test changes incrementally to avoid breaking legitimate filesystem or networking behavior.
```

**Expected AI output:** A container privilege and escape-blast-radius assessment.

**Optional follow-up prompts:**
- `Generate the strictest Kubernetes securityContext compatible with the application's observed runtime requirements.`

---

## Kubernetes Security

### 60. Kubernetes Workload Security Review

**Purpose:** Review Kubernetes manifests and workload-level security controls.

**Context:** Spring Boot workloads deployed to Kubernetes/EKS.

**When to use it:** Before deployment or cluster hardening.

**Complete prompt:**

```text
Review the current Kubernetes manifests and Helm/Kustomize configuration for workload security.

Evaluate namespaces, service accounts, automounted tokens, RBAC, pod/container securityContext, capabilities, privileged settings, seccomp, read-only filesystem, secrets/config maps, volume mounts, hostPath/hostNetwork/hostPID usage, resource limits, probes, image references, imagePullPolicy, and admission-policy assumptions.

Identify settings that unnecessarily increase pod, namespace, or node compromise impact. Verify whether application pods need Kubernetes API access at all.

Classify findings as Critical, High, Medium, or Low. Explain the exploitation path before fixes.

Recommend incremental manifest changes and distinguish workload controls from cluster-wide controls. Validate manifests and deployment assumptions where tooling is available.
```

**Expected AI output:** A workload-level Kubernetes security assessment with least-privilege manifests.

**Optional follow-up prompts:**
- `Remove unnecessary service-account token access and prove the application does not depend on the Kubernetes API.`

### 61. Kubernetes RBAC Review

**Purpose:** Find excessive Kubernetes API privileges and escalation paths.

**Context:** Roles, ClusterRoles, bindings, and service accounts.

**When to use it:** For cluster or application namespace reviews.

**Complete prompt:**

```text
Review Kubernetes RBAC associated with the current application and deployment tooling.

Map service accounts and human/automation identities to Roles, ClusterRoles, RoleBindings, and ClusterRoleBindings. Identify wildcard verbs/resources, secrets access, pod exec/attach, workload creation, RBAC modification, token creation, impersonation, node access, and other permissions that enable privilege escalation.

Determine whether namespace-scoped access can replace cluster-scoped access and whether deployment tooling has broader runtime permissions than necessary.

Classify findings as Critical, High, Medium, or Low. Explain escalation chains before remediation.

Propose least-privilege RBAC while accounting for controllers that legitimately require broader permissions. Clearly separate application runtime permissions from CI/CD or operator permissions.
```

**Expected AI output:** An RBAC privilege graph and escalation-aware least-privilege plan.

**Optional follow-up prompts:**
- `Generate namespace-scoped RBAC for the application service account based only on its demonstrated Kubernetes API needs.`

### 62. Kubernetes Network Policy Review

**Purpose:** Limit lateral movement and unintended ingress/egress.

**Context:** Kubernetes cluster supporting NetworkPolicy.

**When to use it:** For multi-service or multi-tenant clusters.

**Complete prompt:**

```text
Review network connectivity required by the current Kubernetes workload and design or assess NetworkPolicies.

Identify legitimate ingress sources and egress destinations including DNS, databases, queues, identity providers, AWS endpoints, telemetry, and internal services. Compare required connectivity with current effective cluster networking.

Identify unrestricted pod-to-pod communication, unnecessary internet egress, broad namespace selectors, and policies that appear restrictive but are ineffective because the CNI does not enforce them or because complementary ingress/egress policies are missing.

Classify findings as Critical, High, Medium, or Low. Explain lateral-movement or exfiltration impact before fixes.

Recommend incremental default-deny and allow policies, preserving required DNS and platform traffic. Explain operational tradeoffs and validation steps.
```

**Expected AI output:** A required-connectivity matrix and enforceable network policy design.

**Optional follow-up prompts:**
- `Generate Kubernetes NetworkPolicies for the current service based on its observed dependencies.`

---

## Cloud and AWS Security

### 63. AWS Application Security Posture Review

**Purpose:** Review AWS controls relevant to the application's actual architecture.

**Context:** Application deployed on AWS.

**When to use it:** Before production, architecture review, or cloud migration.

**Complete prompt:**

```text
Using the current repository, infrastructure configuration, and authorized read-only AWS context, perform an application-focused AWS security review.

Map the AWS services the application uses and assess IAM, VPC/network exposure, load balancers/API gateways, security groups, S3, databases, KMS, Secrets Manager/Parameter Store, logging, monitoring, container/EKS/ECS controls, backups, encryption, and cross-account access as applicable.

Do not produce a generic AWS checklist. Focus on the deployed architecture and identify realistic paths from external compromise, workload compromise, leaked credentials, or misconfiguration to sensitive assets.

Classify findings as Critical, High, Medium, or Low. Explain attack paths and blast radius before remediation.

Recommend incremental controls using managed AWS capabilities where they improve security and operability. Clearly identify changes that require infrastructure-owner approval.
```

**Expected AI output:** An architecture-specific AWS attack-path review.

**Optional follow-up prompts:**
- `Reassess the architecture assuming one application pod or task has been fully compromised.`

### 64. S3 Data Exposure Review

**Purpose:** Prevent unintended public or cross-account access to application data.

**Context:** Application reads/writes S3.

**When to use it:** For file storage, exports, backups, or static assets.

**Complete prompt:**

```text
Review S3 security for buckets used by the current application using authorized read-only AWS context where available.

Evaluate Block Public Access, bucket policies, IAM access, ACL usage, object ownership, encryption, KMS permissions, presigned URLs, lifecycle, versioning where relevant, logging, cross-account access, and application object-key authorization.

Identify public exposure, overly broad principals, confused-deputy risks, long-lived presigned URLs, predictable object keys combined with weak application authorization, and KMS policies that defeat bucket restrictions.

Classify findings as Critical, High, Medium, or Low. Explain data exposure or modification paths before fixes.

Recommend least-privilege bucket/IAM policies and application-level authorization without relying on obscurity of object keys.
```

**Expected AI output:** An S3 access-path analysis covering both AWS and application authorization.

**Optional follow-up prompts:**
- `Design a secure presigned-download flow with appropriate lifetime and object-level authorization.`

---

## CI/CD Security

### 65. CI/CD Pipeline Security Review

**Purpose:** Protect build and deployment pipelines from credential theft and unauthorized release changes.

**Context:** GitHub Actions, GitLab CI, Jenkins, AWS CodePipeline/CodeBuild, or similar.

**When to use it:** Before production or after pipeline expansion.

**Complete prompt:**

```text
Review the current CI/CD pipeline as a privileged production security boundary.

Evaluate source triggers, branch/tag protections represented in configuration, third-party actions/plugins, version pinning, runner trust, build isolation, secret exposure, artifact integrity, deployment credentials, environment approvals, OIDC/federated cloud credentials, cache poisoning, pull-request behavior, and permissions granted to pipeline jobs.

Identify paths by which untrusted code or a compromised dependency could steal secrets, modify artifacts, or deploy to production.

Classify findings as Critical, High, Medium, or Low. Explain the pipeline attack path before remediation.

Prefer short-lived federated credentials, immutable artifacts, least-privilege job permissions, and separation between build and deploy authority. Recommend incremental hardening and validation steps.
```

**Expected AI output:** A pipeline attack-path review and privilege-minimization plan.

**Optional follow-up prompts:**
- `Redesign cloud authentication in the pipeline to use short-lived OIDC federation instead of stored long-lived keys.`

### 66. Artifact Integrity and Provenance Review

**Purpose:** Ensure the artifact deployed is the artifact that was reviewed and built.

**Context:** Build, registry, and deployment pipeline.

**When to use it:** For software supply-chain hardening.

**Complete prompt:**

```text
Review artifact integrity from source commit through build, container/package publication, promotion, and production deployment.

Determine whether artifacts are rebuilt between environments, whether mutable tags are used, how digests/checksums are tracked, who can overwrite artifacts, how provenance is recorded, and whether deployment references immutable artifacts.

Identify opportunities for artifact substitution, tag mutation, untrusted rebuilds, registry overwrite, or loss of traceability between commit and production.

Classify findings as Critical, High, Medium, or Low. Explain the tampering path before remediation.

Recommend an incremental immutable-artifact and provenance model compatible with the existing CI/CD platform. Include SBOM/signing/attestation only where they provide verifiable value rather than ceremonial metadata.
```

**Expected AI output:** A source-to-production integrity chain with concrete tampering controls.

**Optional follow-up prompts:**
- `Change deployment references from mutable image tags to immutable digests while preserving release traceability.`

---

## Production Security Review

### 67. Pre-Production Security Gate

**Purpose:** Decide whether a release has security blockers.

**Context:** Release candidate for a production Spring Boot service.

**When to use it:** Immediately before production approval.

**Complete prompt:**

```text
Perform a release-focused production security gate on the current release candidate.

Review only issues material to safe production deployment: authentication, authorization, exposed endpoints, secret handling, vulnerable dependencies, TLS, security headers where applicable, database permissions, cloud/workload identity, container/Kubernetes privileges, logging of sensitive data, critical input-handling paths, and production configuration.

Classify findings as Critical, High, Medium, or Low. Separate:
- Release blockers: Critical or High issues with credible exploit paths or unacceptable blast radius.
- Required near-term remediation: material issues that can be accepted temporarily with explicit controls.
- Hardening opportunities: lower-risk improvements.

For every blocker, provide evidence, attack path, smallest safe fix, and validation required. Do not block release on theoretical or cosmetic findings.

Run available builds and relevant security/regression tests. Return a clear Go / Conditional Go / No-Go recommendation with rationale and residual risk.
```

**Expected AI output:** A defensible release security decision rather than an unprioritized issue list.

**Optional follow-up prompts:**
- `Fix only the release blockers, run validation, and reassess the Go/No-Go decision.`

### 68. Production Configuration Security Review

**Purpose:** Detect insecure differences between development and production settings.

**Context:** Spring profiles, environment variables, deployment configuration, and cloud settings.

**When to use it:** Before deploying a new environment or configuration change.

**Complete prompt:**

```text
Review production-specific configuration and compare it with development/test defaults.

Inspect Spring profiles, application configuration, environment variables, logging levels, actuator exposure, debug settings, error details, CORS, security rules, TLS/proxy settings, database credentials/permissions, feature flags, management endpoints, test users, seed data, mock integrations, and cloud/container configuration.

Identify development conveniences that can become production vulnerabilities and configuration overrides that weaken code-level security controls.

Classify findings as Critical, High, Medium, or Low. Explain the production attack or disclosure path before fixes.

Recommend incremental configuration changes and automated assertions that prevent insecure production settings from reappearing.
```

**Expected AI output:** A production-versus-development configuration delta with guardrails.

**Optional follow-up prompts:**
- `Add automated startup or CI checks for the production configuration invariants that should never be violated.`

---

## Incident Response

### 69. Security Incident Code and Configuration Triage

**Purpose:** Rapidly determine likely exploit paths and containment actions during an incident.

**Context:** A suspected compromise with current repository/configuration and available incident evidence.

**When to use it:** During active security investigation.

**Complete prompt:**

```text
Act as a senior application security engineer supporting an active incident. Review the current repository, configuration, and available incident evidence to identify plausible exploit paths and immediate containment options.

Prioritize preserving evidence and reducing attacker access. Distinguish confirmed facts, strong indicators, hypotheses, and unknowns. Trace relevant authentication, authorization, exposed endpoints, secrets, dependencies, outbound access, persistence, and cloud permissions.

Classify discovered weaknesses as Critical, High, Medium, or Low, but prioritize containment by current attacker utility rather than severity label alone.

Recommend reversible containment actions first where possible: credential/key rotation, access revocation, endpoint restriction, feature disablement, network restriction, or deployment rollback. Explain operational impact and evidence-loss risk for each action.

Do not make destructive changes or rotate production credentials without explicit authorization. Return investigation hypotheses, containment priorities, evidence to preserve, remediation items, and validation steps.
```

**Expected AI output:** An incident-oriented triage plan that separates evidence, hypotheses, containment, and remediation.

**Optional follow-up prompts:**
- `Build a timeline from the available logs and correlate it with the suspected vulnerable code path.`

### 70. Credential Exposure Response Review

**Purpose:** Respond correctly when a secret, token, or private key may have been exposed.

**Context:** Suspected credential leak in source, logs, CI, artifact, or runtime.

**When to use it:** Immediately after discovering credential exposure.

**Complete prompt:**

```text
Assess the current suspected credential exposure and produce a containment and recovery plan.

Identify the credential type, where it was exposed, systems that may have received copies, permissions/blast radius, lifetime, rotation/revocation mechanism, dependent applications, and available audit evidence.

Do not reproduce the credential value. Treat removal from source or logs as insufficient if the credential may already have been copied.

Classify the underlying exposure as Critical, High, Medium, or Low and explain the blast radius. Prioritize revocation/rotation, replacement deployment, access review, audit-log investigation, and repository/history cleanup as appropriate.

Distinguish actions that can be automated safely from actions requiring explicit production approval. Include post-incident prevention measures such as short-lived credentials and secret scanning.
```

**Expected AI output:** A rotation-first response plan with blast-radius and dependency analysis.

**Optional follow-up prompts:**
- `Identify every configuration location and deployment that must be updated when this credential is rotated.`

---

## Security Architecture Review and Compliance Readiness

### 71. Formal Security Architecture Review

**Purpose:** Produce an engineering-grade architecture decision review for security stakeholders.

**Context:** A system approaching design approval or major architectural change.

**When to use it:** For architecture boards, security sign-off, or major migrations.

**Complete prompt:**

```text
Produce a formal security architecture review of the current system suitable for senior engineering and security stakeholders.

Derive the architecture from the current repository and deployment configuration. Cover assets, actors, trust boundaries, identity model, authentication, authorization, data classification, data flows, cryptography, secrets, external dependencies, network exposure, cloud/workload identity, observability, administrative paths, and failure/incident considerations.

Identify risks before controls and classify findings as Critical, High, Medium, or Low. For each material risk, document evidence, attack scenario, current mitigation, residual risk, recommended treatment, and architectural tradeoffs.

Separate tactical remediation from target-state architectural improvements. Avoid recommending wholesale redesign unless the current architecture cannot meet a required security property.

Return explicit architecture decisions, open questions, accepted risks requiring owner approval, and a prioritized remediation roadmap.
```

**Expected AI output:** A stakeholder-ready architecture security review with decisions and residual risk.

**Optional follow-up prompts:**
- `Convert the review into architecture decision records for the three most consequential security choices.`

### 72. Compliance Readiness Engineering Gap Analysis

**Purpose:** Translate compliance requirements into concrete engineering controls without pretending to perform certification.

**Context:** A system preparing for an internal or external compliance program.

**When to use it:** Before formal audit preparation.

**Complete prompt:**

```text
Perform an engineering-focused compliance readiness gap analysis for the current system against the compliance framework or control set already present in the workspace.

Map applicable technical requirements to concrete evidence in code, configuration, CI/CD, cloud infrastructure, identity systems, logging, audit trails, encryption, access control, vulnerability management, backup/recovery, and change management artifacts.

Do not claim certification or legal compliance. Distinguish:
- Implemented and evidenced controls.
- Partially implemented controls.
- Missing controls.
- Controls that are organizational/process responsibilities rather than application code.

For technical gaps, classify security impact as Critical, High, Medium, or Low separately from audit importance. Explain risks before recommending fixes and prioritize controls that improve real security rather than documentation alone.

Return an evidence matrix, engineering remediation backlog, missing evidence, and items requiring compliance/legal interpretation.
```

**Expected AI output:** An evidence-based readiness matrix that separates engineering security from audit semantics.

**Optional follow-up prompts:**
- `Create technical acceptance criteria for each missing engineering control without inventing policy requirements.`

---

## Advanced Security Workflows

### 73. Multi-Tenant Isolation Review

**Purpose:** Detect cross-tenant data and privilege leakage.

**Context:** SaaS application with tenant-scoped data and identities.

**When to use it:** Before onboarding multiple customers or changing tenant architecture.

**Complete prompt:**

```text
Review the current system for tenant-isolation security.

Trace how tenant identity is established, propagated, validated, and enforced across HTTP requests, JWT claims, sessions, service methods, repositories, caches, async jobs, messages, object storage, logs, and database queries.

Identify client-controlled tenant selectors, missing tenant predicates, cache-key collisions, background jobs without tenant context, privileged cross-tenant administrative paths, and places where tenant isolation depends only on UI or gateway behavior.

Classify findings as Critical, High, Medium, or Low. Explain the exact cross-tenant attack or leakage path before remediation.

Recommend defense-in-depth appropriate to the persistence model. Avoid blindly adding tenant filters if they can be bypassed by alternate data-access paths. Add cross-tenant negative tests for high-risk operations.
```

**Expected AI output:** A tenant-context propagation and enforcement review with cross-tenant tests.

**Optional follow-up prompts:**
- `Design repository/service invariants that make accidental cross-tenant queries difficult to introduce.`

### 74. Actuator and Management Endpoint Security Review

**Purpose:** Prevent management endpoints from leaking internals or enabling privileged actions.

**Context:** Spring Boot Actuator enabled.

**When to use it:** Before production or observability changes.

**Complete prompt:**

```text
Review Spring Boot Actuator and management endpoint security in the current project and deployment.

Determine which endpoints are enabled, exposed over HTTP or JMX, bound to which network/interface/port, protected by which SecurityFilterChain, and reachable through ingress/load balancers. Review health details, env/configprops, heapdump, threaddump, loggers, mappings, metrics, shutdown, and custom actuator endpoints as applicable.

Identify unauthenticated or overly broad access, sensitive configuration disclosure, secrets in environment output, dangerous write operations, and management ports unintentionally exposed externally.

Classify findings as Critical, High, Medium, or Low. Explain the attack or disclosure path before fixes.

Recommend the minimum exposed endpoint set, appropriate network isolation, authorization, and sanitization. Add configuration tests where practical.
```

**Expected AI output:** An effective actuator exposure map and production-safe management policy.

**Optional follow-up prompts:**
- `Separate management traffic onto an internal-only path while preserving health checks required by the platform.`

### 75. Security Headers Review

**Purpose:** Verify browser-facing responses use appropriate defensive headers.

**Context:** Spring Security web application or API serving browser clients.

**When to use it:** For browser-exposed applications.

**Complete prompt:**

```text
Review browser security headers produced by the current application and edge infrastructure.

Evaluate HSTS, Content-Security-Policy, frame-ancestors/X-Frame-Options, X-Content-Type-Options, Referrer-Policy, Permissions-Policy, cache-control for sensitive responses, and any legacy headers still configured.

Determine the effective header values after proxies/gateways and identify duplicate, contradictory, missing, or overly permissive policies. Base recommendations on the application's actual frontend behavior; do not propose a CSP that would break required scripts/styles without a migration strategy.

Classify material findings as Critical, High, Medium, or Low. Explain the browser attack mitigated and tradeoffs before fixes.

Recommend incremental header hardening and tests that assert critical response headers.
```

**Expected AI output:** A browser-defense header assessment with deployable CSP/header guidance.

**Optional follow-up prompts:**
- `Design a staged Content-Security-Policy rollout using report-only mode before enforcement.`

### 76. Rate Limiting and Resource Abuse Review

**Purpose:** Identify security-relevant denial-of-service and brute-force opportunities.

**Context:** Public or partner APIs.

**When to use it:** When endpoints perform expensive work or authentication-sensitive operations.

**Complete prompt:**

```text
Review the current system for application-layer resource abuse and rate-limiting needs.

Identify endpoints that enable password/login attempts, token operations, search, exports, file processing, expensive database queries, outbound calls, report generation, bulk operations, or other attacker-amplifiable work.

Assess existing controls at application, gateway, WAF, load balancer, queue, and infrastructure layers. Identify limits that can be bypassed by alternate routes, spoofed identifiers, distributed clients, or asynchronous fan-out.

Classify findings as Critical, High, Medium, or Low based on availability, cost, brute-force, and downstream impact. Explain the abuse path before fixes.

Recommend layered controls and backpressure appropriate to the architecture. Explain fairness, false-positive, state-management, and distributed-system tradeoffs.
```

**Expected AI output:** A resource-abuse model and layered throttling/backpressure strategy.

**Optional follow-up prompts:**
- `Implement bounded pagination and request-size controls for the highest-cost API paths and add tests.`

### 77. Database Security and Service Account Review

**Purpose:** Limit database blast radius after application compromise.

**Context:** Spring Boot application using a relational database through Jakarta Persistence/JDBC.

**When to use it:** Before production or after schema/privilege changes.

**Complete prompt:**

```text
Review database security from the application's perspective.

Inspect datasource configuration, credential management, TLS, database user privileges, schema ownership, migration credentials, runtime DDL capability, read/write separation where present, stored procedures, native queries, connection-pool settings relevant to security, and sensitive-data storage.

Determine whether the runtime application account can create/drop schema objects, access unrelated schemas/databases, manage users, read system catalogs unnecessarily, or perform administrative operations.

Classify findings as Critical, High, Medium, or Low. Explain what an attacker could do after compromising the application before recommending changes.

Recommend separate migration and runtime identities where appropriate, least-privilege grants, secure credential rotation, and encryption controls. Avoid breaking Hibernate/JPA behavior without verifying required privileges.
```

**Expected AI output:** A database blast-radius and runtime-privilege assessment.

**Optional follow-up prompts:**
- `Derive the minimum runtime database privileges required by the current JPA repositories and application behavior.`

### 78. Sensitive Data Exposure and Data Minimization Review

**Purpose:** Reduce unnecessary collection, storage, transmission, and logging of sensitive data.

**Context:** Application handling personal, financial, confidential, or credential-adjacent data.

**When to use it:** During architecture, privacy, or production security reviews.

**Complete prompt:**

```text
Review the current system for sensitive-data exposure and unnecessary data retention.

Identify sensitive fields and trace where they enter, are transformed, persisted, cached, logged, transmitted, exported, backed up, and returned through APIs. Review DTOs, entities, serializers, toString implementations, exception messages, audit records, telemetry, and test fixtures.

Identify data collected but not used, fields returned to clients without need, secrets or sensitive values copied across boundaries, excessive retention, and encryption applied without access minimization.

Classify findings as Critical, High, Medium, or Low. Explain disclosure impact and likely exposure paths before remediation.

Prefer data minimization and access restriction before adding cryptography. Recommend incremental schema/API/logging changes and identify compatibility or retention-policy dependencies requiring human decisions.
```

**Expected AI output:** A sensitive-data flow and minimization assessment.

**Optional follow-up prompts:**
- `Create a field-level data classification table for the current API and persistence model.`

### 79. Webhook Security Review

**Purpose:** Prevent spoofing, replay, and SSRF in inbound/outbound webhook integrations.

**Context:** Applications receiving or sending webhooks.

**When to use it:** When integrating external event callbacks.

**Complete prompt:**

```text
Review inbound and outbound webhook security in the current system.

For inbound webhooks, evaluate source authentication, signature/MAC verification, canonicalization, timestamp/replay protection, secret rotation, request-size limits, parsing, idempotency, and authorization of resulting actions.

For outbound webhooks, evaluate destination validation, SSRF controls, credential/signature generation, secret storage, retry behavior, redirect handling, data minimization, and tenant isolation.

Classify findings as Critical, High, Medium, or Low. Explain spoofing, replay, privilege, or exfiltration attack paths before remediation.

Recommend incremental controls and add tests using valid, invalid, stale, replayed, and tampered webhook requests.
```

**Expected AI output:** A bidirectional webhook security model with replay and destination controls.

**Optional follow-up prompts:**
- `Implement constant-time signature verification and replay protection for the current inbound webhook format.`

### 80. Security Test Coverage Design

**Purpose:** Turn security requirements and discovered risks into durable automated regression tests.

**Context:** Existing Spring Boot test suite.

**When to use it:** After a security review or before hardening work.

**Complete prompt:**

```text
Review the current security controls and automated tests, then design a focused security regression test suite.

Prioritize tests for authentication rejection, authorization denial, object-level access control, JWT validation, CSRF/CORS where applicable, input validation, injection defenses, sensitive error responses, tenant isolation, privileged method security, and any High/Critical findings already identified in the workspace.

Avoid tests that merely assert framework defaults without protecting an application-specific security requirement. Prefer integration tests for effective security behavior and focused unit tests for complex authorization or validation components.

For each proposed test, state the security invariant it protects and the regression it would catch. Implement the highest-value missing tests using the project's existing test conventions. Use explicit Java imports, compile, and run the relevant Gradle test tasks.

Return coverage gaps, tests added, test results, and security properties still requiring non-automated verification.
```

**Expected AI output:** A risk-driven security test suite tied to explicit invariants.

**Optional follow-up prompts:**
- `Add negative authorization tests for every privileged endpoint that currently has only happy-path coverage.`

---

## Chapter Completion Checklist

This Version 1 chapter contains **80 distinct production-security prompts** spanning architecture, threat modeling, Spring Security, OAuth/OIDC, JWT, authorization, cloud, containers, Kubernetes, supply chain, secure coding, production readiness, incident response, and compliance readiness. The prompts are intentionally context-aware and designed for engineering agents operating directly against the current workspace and authorized infrastructure context.
