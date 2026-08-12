# Spring Boot Prompt Library

> **AI Engineering Workbench --- Version 1**

This chapter contains reusable, production-oriented prompts for
experienced engineers working with Java 21, Spring Boot 3.x, Gradle, and
Jakarta APIs. Replace bracketed placeholders with repository-specific
information before use.

## Project Creation

### 1. Create a production Spring Boot service skeleton

**Purpose:** create a production Spring Boot service skeleton.

**When to use it:** Starting a new service that needs a maintainable
baseline.

**Complete prompt:**

``` text
Act as a senior Java platform engineer. Create a new production-grade Spring Boot 3.x service using Java 21 and Gradle.

Service name: [SERVICE_NAME]
Business responsibility: [RESPONSIBILITY]
Required integrations: [DATABASE / MESSAGING / EXTERNAL APIs / NONE]

Requirements:
- Use the latest compatible Spring Boot 3.x release unless the repository constrains the version.
- Use Gradle and the Gradle Wrapper.
- Use Java 21 toolchains.
- Use package-by-feature unless there is a concrete reason not to.
- Use constructor injection only.
- Use Jakarta APIs; use Jakarta Persistence if persistence is required.
- Include Actuator and a minimal health/readiness setup appropriate for production.
- Configure tests with JUnit Jupiter.
- Add only dependencies justified by the stated requirements.
- Include .gitignore, application.yml, and a concise README with build/run/test commands.
- Do not add speculative abstractions, interfaces, or infrastructure.
- Explain important architectural decisions before implementing them.
- Create the project incrementally, compile it, and run all tests after each meaningful step.
- If a decision depends on missing information, state the assumption explicitly.

Return:
1. Proposed package/module structure.
2. Dependency rationale.
3. Files created or changed.
4. Implementation.
5. Build and test results.
6. Remaining production concerns.
```

**Expected AI output:** An idiomatic, compiling Spring Boot project with
a small dependency footprint, clear structure, tests, and rationale.

**Optional follow-up prompts:** -
`Add PostgreSQL persistence without changing unrelated packages.` -
`Add a Dockerfile and production JVM defaults, then run the test suite.`

**Notes / cautions:** Do not let the AI generate unused layers merely
because they are common in sample applications.

### 2. Modernize an existing Spring Boot project baseline

**Purpose:** modernize an existing Spring Boot project baseline.

**When to use it:** An older service needs a controlled baseline upgrade
before feature work.

**Complete prompt:**

``` text
Review this existing Spring Boot repository and propose the smallest safe modernization path to Java 21 and Spring Boot 3.x.

Repository: [ATTACH OR PROVIDE REPOSITORY]

Inspect before changing anything:
- Current Java, Spring Boot, Gradle, plugin, and dependency versions.
- javax.* usage that must migrate to jakarta.*.
- Deprecated Spring APIs and configuration properties.
- Test framework compatibility.
- Build plugins, annotation processors, generated sources, and CI assumptions.
- Runtime/container constraints.

First produce a migration plan grouped into:
A. Required compatibility changes.
B. Recommended maintainability changes.
C. Optional improvements that should NOT block the migration.

Do not modify code until the plan is presented. Then implement only group A unless I approve more.
Keep changes incremental and reviewable. Preserve behavior. Do not combine architectural refactoring with framework migration.
After each stage, run ./gradlew clean test and any existing integration-test task. Diagnose failures rather than suppressing tests.
For every dependency/version change, explain why it is required.
```

**Expected AI output:** A risk-ranked migration plan followed by a
minimal, test-verified Java 21/Spring Boot 3.x upgrade.

**Optional follow-up prompts:** -
`Now implement only the required compatibility changes.` -
`Review the completed migration for accidental behavioral changes.`

**Notes / cautions:** Framework migration and architecture redesign
should normally be separate changesets.

## Gradle

### 3. Audit a Spring Boot Gradle build

**Purpose:** audit a Spring Boot Gradle build.

**When to use it:** A build.gradle or build.gradle.kts has accumulated
plugins, dependencies, and custom tasks.

**Complete prompt:**

``` text
Review the Gradle build for this Java 21 / Spring Boot 3.x service as a senior build engineer.

Build files: [PASTE OR ATTACH]

Evaluate:
- Plugin versions and whether Spring Boot dependency management is being used correctly.
- Java 21 toolchain configuration.
- Redundant, unused, duplicated, or incorrectly scoped dependencies.
- api vs implementation vs compileOnly vs annotationProcessor vs runtimeOnly vs testImplementation.
- Reproducibility and dependency locking/version catalogs if appropriate.
- Test task configuration and JUnit Platform usage.
- Custom tasks that duplicate standard Gradle/Spring Boot behavior.
- Wrapper version and CI compatibility.
- Build performance issues that are visible from configuration.

Do not rewrite the build initially. Produce a prioritized findings table with severity, evidence, and exact recommendation.
Then propose the smallest patch for high-value issues only.
Do not introduce a version catalog, convention plugin, or multi-project structure unless the repository complexity justifies it.
After approved changes, run ./gradlew clean test and report the exact result.
```

**Expected AI output:** A focused build audit and minimal patch that
improves correctness, maintainability, and reproducibility.

**Optional follow-up prompts:** -
`Check the dependency graph for conflicting versions.` -
`Explain which dependencies can move to runtimeOnly or testImplementation.`

**Notes / cautions:** Build-file cleverness is usually a maintenance
cost; prefer standard Gradle conventions.

### 4. Diagnose Gradle dependency conflicts

**Purpose:** diagnose Gradle dependency conflicts.

**When to use it:** The service has classpath errors, NoSuchMethodError,
version drift, or unexplained transitive dependencies.

**Complete prompt:**

``` text
Diagnose this Spring Boot 3.x Gradle dependency problem without guessing.

Symptoms:
[PASTE ERROR / STACK TRACE]

Relevant build files:
[PASTE OR ATTACH]

Use Gradle dependencyInsight/dependencies tasks where useful. Determine:
1. Which dependency introduces the conflicting artifact.
2. Which version Spring Boot's dependency management expects.
3. Whether an explicit version is overriding the managed version.
4. Whether exclusion, alignment, a constraint, or removal is the correct fix.
5. Whether the conflict is compile-time, test-time, or runtime.

Prefer removing unnecessary explicit versions over forcing versions.
Do not globally exclude a transitive dependency unless you can explain why that is safe.
Make the smallest change that restores a coherent dependency graph.
Run clean tests after the change and show the relevant dependencyInsight result proving the resolution.
```

**Expected AI output:** A root-cause dependency analysis backed by
Gradle output and a minimal verified fix.

**Optional follow-up prompts:** -
`Check whether any other explicitly versioned Spring ecosystem libraries should be unpinned.` -
`Add a dependency constraint only if removal of the override is insufficient.`

**Notes / cautions:** Do not treat dependency forcing as the default
solution.

## Configuration

### 5. Design type-safe application configuration

**Purpose:** design type-safe application configuration.

**When to use it:** A service has a growing set of related configuration
properties.

**Complete prompt:**

``` text
Refactor the following Spring Boot configuration into type-safe, maintainable configuration properties.

Current configuration/code:
[PASTE application.yml AND RELATED CODE]

Use Spring Boot 3.x and Java 21.
Requirements:
- Prefer @ConfigurationProperties over scattered @Value fields.
- Group properties around a cohesive external concern.
- Use immutable configuration where practical.
- Validate required values with Jakarta Validation.
- Preserve Spring Boot relaxed binding conventions.
- Do not expose secrets through toString(), logs, Actuator, or error messages.
- Distinguish required settings from settings with defensible defaults.
- Keep environment-specific values outside code.

First show the proposed property namespace and Java type. Explain why the grouping is cohesive.
Then implement the smallest refactor and update tests.
Run ./gradlew test and report binding/validation coverage.
```

**Expected AI output:** Validated, type-safe configuration with a clear
namespace and tests.

**Optional follow-up prompts:** -
`Add a configuration binding test for missing required values.` -
`Review which defaults are safe versus dangerous in production.`

**Notes / cautions:** A default that hides missing production
configuration can be worse than failing fast.

### 6. Review application.yml for production safety

**Purpose:** review application.yml for production safety.

**When to use it:** Before deployment, configuration needs a focused
safety and maintainability review.

**Complete prompt:**

``` text
Review this Spring Boot 3.x application.yml/application.properties as if it were going to production.

Configuration:
[PASTE OR ATTACH]

Check specifically for:
- Embedded credentials, tokens, private URLs, or other secrets.
- Dangerous defaults.
- Environment-specific values committed to source.
- Incorrect property names or obsolete Spring Boot properties.
- Logging settings that could leak sensitive data.
- Database pool and timeout settings.
- HTTP/server timeout and graceful shutdown settings.
- Actuator exposure.
- JPA/Hibernate DDL behavior.
- Profile activation mistakes.
- Placeholder syntax and missing-value behavior.

Classify findings as Critical, High, Medium, or Low.
For each finding include the exact property, risk, and recommended replacement.
Do not invent values when workload information is missing; identify what must be measured.
Provide a minimal corrected configuration only after the review.
```

**Expected AI output:** A production-focused configuration audit with a
minimal corrected example.

**Optional follow-up prompts:** -
`Separate environment-neutral defaults from deployment-supplied values.` -
`Review the Kubernetes/EC2 environment variables that map to these properties.`

**Notes / cautions:** Configuration values such as pool sizes and
timeouts should be workload-driven, not copied from generic examples.

## Profiles

### 7. Simplify Spring profile usage

**Purpose:** simplify Spring profile usage.

**When to use it:** Profile-specific beans and YAML files have become
hard to reason about.

**Complete prompt:**

``` text
Review the use of Spring profiles in this Spring Boot 3.x service.

Inputs:
[PASTE PROFILE ANNOTATIONS, application-*.yml FILES, AND RELEVANT BEANS]

Identify:
- Profiles representing deployment environments versus actual behavioral modes.
- Beans whose selection would be clearer through explicit configuration properties.
- Profile expressions that are difficult to test or reason about.
- Duplicate configuration across profile files.
- Tests that depend accidentally on developer-local profiles.

Recommend a simpler model that minimizes profile-specific code.
Prefer configuration properties and externalized values when environment differences are data, not behavior.
Do not remove a profile when it legitimately selects a different implementation.
Show the migration incrementally and include tests proving bean selection for each supported mode.
Run the test suite after changes.
```

**Expected AI output:** A simplified profile strategy with explicit
bean-selection tests.

**Optional follow-up prompts:** -
`Replace the selected profile with a property-driven conditional bean.` -
`Create tests that prove no ambiguous bean configuration is possible.`

**Notes / cautions:** Profiles are useful, but environment names
scattered through code create hidden coupling.

### 8. Create safe local/test/production configuration separation

**Purpose:** create safe local/test/production configuration separation.

**When to use it:** A new service needs clear environment separation
without duplicating entire configuration files.

**Complete prompt:**

``` text
Design the configuration/profile strategy for a Spring Boot 3.x service with local development, automated tests, and production deployment.

Constraints:
[DESCRIBE DATABASE, EXTERNAL SERVICES, SECRET STORE, DEPLOYMENT PLATFORM]

Produce:
- A small environment-neutral application.yml.
- Only the profile-specific files that are genuinely needed.
- A rule for which values come from environment variables or secret management.
- Test configuration that is deterministic and does not depend on a developer machine.
- A strategy for local substitutes such as Testcontainers or local endpoints.

Do not put production secrets or production hostnames in source control.
Do not create profile-specific Java classes unless behavior actually differs.
Explain precedence and activation so another engineer can predict which value wins.
Include a test approach for configuration binding and critical bean selection.
```

**Expected AI output:** A minimal profile/configuration scheme that is
predictable across local, test, and production environments.

**Optional follow-up prompts:** -
`Adapt this design for Kubernetes ConfigMaps and Secrets.` -
`Add Testcontainers-based integration-test configuration.`

**Notes / cautions:** Keep the number of profiles small; deployment
platforms already provide an environment/configuration layer.

## Dependency Management

### 9. Review dependencies for necessity and risk

**Purpose:** review dependencies for necessity and risk.

**When to use it:** A service needs dependency hygiene before release or
upgrade.

**Complete prompt:**

``` text
Perform a dependency hygiene review of this Spring Boot 3.x / Java 21 Gradle project.

Inputs:
[ATTACH build files AND, IF AVAILABLE, ./gradlew dependencies OUTPUT]

For each direct dependency determine:
- What production or test capability requires it.
- Whether Spring Boot already provides/manages an alternative.
- Whether the scope is correct.
- Whether it appears unused or duplicated.
- Whether an explicit version should be removed in favor of Boot dependency management.
- Whether it materially expands runtime/security surface.

Do not remove a dependency solely because static inspection cannot find direct imports; account for reflection, starters, JDBC drivers, logging providers, annotation processors, and runtime integrations.
Return a keep/remove/change-scope/unpin recommendation with rationale.
Then propose a minimal patch and run clean tests after approved changes.
```

**Expected AI output:** A defensible dependency inventory and a low-risk
cleanup patch.

**Optional follow-up prompts:** -
`Inspect transitive dependencies introduced by the largest starters.` -
`Identify dependencies that are only required in tests.`

**Notes / cautions:** Runtime frameworks often use dependencies
indirectly; validate before removing them.

### 10. Choose between Spring starters and individual dependencies

**Purpose:** choose between Spring starters and individual dependencies.

**When to use it:** Engineers are deciding whether a starter is too
broad for a service.

**Complete prompt:**

``` text
Evaluate whether this Spring Boot service should use the following starter or replace it with narrower dependencies.

Starter/dependencies: [NAME]
Use case: [WHAT THE SERVICE ACTUALLY NEEDS]
Current build: [PASTE RELEVANT DEPENDENCIES]

Analyze:
- What the starter contributes directly and transitively.
- Which auto-configurations are relevant.
- Operational/security surface added.
- Version-management benefits.
- Maintenance cost of manually assembling equivalent dependencies.
- Whether exclusions would make the build more fragile.

Recommend the simplest maintainable choice. Do not optimize for the fewest JARs unless there is a measurable reason.
If recommending a change, provide the exact Gradle patch and verification commands.
Run tests after implementation.
```

**Expected AI output:** A concrete starter-versus-manual dependency
decision with tradeoffs and build changes.

**Optional follow-up prompts:** -
`Show which auto-configurations become active because of this starter.` -
`Check startup condition reports for unwanted auto-configuration.`

**Notes / cautions:** Removing starters can trade a small classpath
reduction for long-term dependency-management burden.

## Database Reverse Engineering

### 11. Reverse-engineer a schema into a persistence model plan

**Purpose:** reverse-engineer a schema into a persistence model plan.

**When to use it:** Integrating a Spring Boot service with an existing
relational schema.

**Complete prompt:**

``` text
Analyze this existing database schema and design a Jakarta Persistence model for Spring Boot 3.x without generating code yet.

Schema/DDL:
[PASTE DDL OR SCHEMA DESCRIPTION]

For each table identify:
- Candidate entity and aggregate ownership.
- Primary key strategy, including composite keys.
- Foreign-key relationships and likely cardinality.
- Nullability and database constraints that must remain authoritative.
- Columns that should map to enums, value types, timestamps, JSON, or remain scalar.
- Legacy naming that should be mapped explicitly rather than copied into Java names.
- Tables that are lookup/reference data, join tables, audit/history tables, or not appropriate as entities.

Flag ambiguous relationships and ask targeted questions rather than guessing.
Avoid bidirectional JPA relationships by default; justify any you recommend.
Do not infer cascade or orphanRemoval merely from foreign keys.
Return a mapping plan, risk list, and proposed entity names only.
```

**Expected AI output:** A careful schema-to-domain mapping plan that
avoids automatic JPA modeling mistakes.

**Optional follow-up prompts:** -
`Generate only the first aggregate's entities from the approved mapping.` -
`Identify queries that may require dedicated read models instead of entity traversal.`

**Notes / cautions:** Database structure does not automatically reveal
domain ownership or transaction boundaries.

### 12. Compare legacy schema constraints with JPA mappings

**Purpose:** compare legacy schema constraints with JPA mappings.

**When to use it:** Existing entities may disagree with the actual
database.

**Complete prompt:**

``` text
Compare these Jakarta Persistence entities with the authoritative database DDL.

Entities:
[PASTE OR ATTACH]

DDL:
[PASTE OR ATTACH]

Find mismatches in:
- Table/column names.
- Length, precision, scale, nullability.
- Primary and foreign keys.
- Unique constraints.
- Enum/string mappings.
- Temporal types.
- Generated/identity/sequence behavior.
- Relationship optionality.
- Index assumptions relevant to known queries.

Do not treat Hibernate schema generation as authoritative.
Classify mismatches into correctness risk, data-loss risk, performance risk, and cosmetic differences.
Propose the smallest entity changes required to match the database.
Do not enable ddl-auto=update as a fix.
Add mapping/integration tests where they can catch future drift.
```

**Expected AI output:** A precise entity-versus-DDL drift report and
safe mapping corrections.

**Optional follow-up prompts:** -
`Create a Testcontainers integration test for the highest-risk mapping.` -
`Review Flyway/Liquibase migrations for the same drift.`

**Notes / cautions:** Production schemas should be changed through
controlled migrations, not Hibernate auto-update.

## Entity Generation

### 13. Generate entities from approved mappings

**Purpose:** generate entities from approved mappings.

**When to use it:** A schema mapping plan is approved and entities need
implementation.

**Complete prompt:**

``` text
Generate Jakarta Persistence entities for the approved mapping below.

Approved mapping:
[PASTE MAPPING PLAN / DDL]

Technical requirements:
- Java 21, Spring Boot 3.x, jakarta.persistence imports.
- Explicit imports; no wildcard imports.
- Use explicit @Table/@Column names where database naming differs from Java naming.
- Choose equals/hashCode semantics deliberately and explain them.
- Avoid Lombok @Data on entities.
- Prefer LAZY relationships where JPA permits and avoid unnecessary relationships.
- Do not add cascade, orphanRemoval, bidirectional associations, or eager fetching without a stated lifecycle reason.
- Preserve database nullability, length, precision, and key generation.
- Use @Version only if optimistic locking is an intentional requirement.
- Keep business validation separate from database mapping unless it is truly the same invariant.

Generate one aggregate at a time. Compile and run tests before continuing to the next aggregate.
```

**Expected AI output:** Conservative, explicit JPA entities that match
the approved schema and compile cleanly.

**Optional follow-up prompts:** -
`Generate mapping tests for these entities.` -
`Review the generated entities for accidental N+1 traversal paths.`

**Notes / cautions:** Entity generation should follow an approved
mapping; it should not invent domain relationships.

### 14. Review JPA entity design

**Purpose:** review JPA entity design.

**When to use it:** Entities exist but need senior-level persistence
review.

**Complete prompt:**

``` text
Review these Jakarta Persistence entities as a senior Spring/JPA engineer.

Entities:
[PASTE OR ATTACH]

Evaluate:
- Identity and equals/hashCode correctness.
- Relationship ownership and cardinality.
- Fetch strategy and N+1 risk.
- Cascades and orphan removal.
- Aggregate boundaries and whether relationships cross them unnecessarily.
- Mutable collections and invariant protection.
- Optimistic locking needs.
- Column mappings, temporal types, enums, precision, and nullability.
- Serialization hazards and accidental REST exposure.
- Whether any entity should instead be a value object or read projection.

Do not refactor immediately. Produce findings ranked by correctness, performance, and maintainability.
For each recommendation explain the concrete failure mode it prevents.
After approval, change one concern at a time and run tests.
```

**Expected AI output:** A prioritized entity-design review tied to
concrete JPA failure modes.

**Optional follow-up prompts:** -
`Implement only the high-severity mapping corrections.` -
`Design repository queries that avoid traversing the risky relationships.`

**Notes / cautions:** JPA entity graphs should model persistence needs
without becoming an unrestricted object graph.

## Repository Generation

### 15. Generate a repository from query requirements

**Purpose:** generate a repository from query requirements.

**When to use it:** A service needs persistence queries without leaking
database concerns into business code.

**Complete prompt:**

``` text
Create a Spring Data JPA repository for the following entity and use cases.

Entity:
[PASTE ENTITY]

Required queries:
[LIST BUSINESS QUERY REQUIREMENTS]

Requirements:
- Spring Boot 3.x, Java 21, explicit imports.
- Extend the narrowest appropriate Spring Data repository abstraction.
- Prefer derived queries only when names remain clear.
- Use @Query for queries whose intent is clearer explicitly.
- Return Optional only for genuinely optional single results; use collections for multi-result queries.
- Do not return entities when a projection/DTO is materially safer or more efficient for a read-only use case.
- Avoid exposing Page where the API does not need total counts; consider Slice when appropriate.
- Identify required indexes from query predicates/orderings, but do not invent migration changes without showing the rationale.
- Flag queries likely to cause N+1 behavior.

Generate repository tests using a real relational database via Testcontainers when query semantics matter.
Run tests and show the results.
```

**Expected AI output:** A small repository API aligned to actual use
cases, with query tests and index observations.

**Optional follow-up prompts:** -
`Convert the read-heavy query to a projection.` -
`Add pagination without forcing a count query when it is unnecessary.`

**Notes / cautions:** Repository methods should express application data
needs, not become a grab bag of every possible query.

### 16. Review Spring Data repository usage

**Purpose:** review Spring Data repository usage.

**When to use it:** Repository code has grown and may contain
inefficient or misplaced operations.

**Complete prompt:**

``` text
Review these Spring Data JPA repositories and their callers.

Code:
[PASTE REPOSITORIES AND RELEVANT SERVICE METHODS]

Check:
- Overly broad repository interfaces.
- Long or ambiguous derived query names.
- Repeated queries that should be consolidated.
- Entity loading when only a few columns are needed.
- N+1 patterns.
- Unbounded findAll-style operations.
- Misuse of save() for already-managed entities.
- Existence checks followed by redundant reads.
- Incorrect Optional usage.
- Pagination/count-query costs.
- Transaction assumptions in repository callers.

For each issue show the actual call path and likely SQL/runtime effect.
Do not propose custom repositories unless Spring Data becomes genuinely inadequate.
Recommend incremental changes and tests that verify query behavior.
```

**Expected AI output:** A call-path-aware repository review focused on
SQL behavior and maintainability.

**Optional follow-up prompts:** -
`Show the expected SQL before and after the top recommendation.` -
`Implement only the N+1 fix and verify it with an integration test.`

**Notes / cautions:** Repository abstractions do not remove the need to
reason about generated SQL.

## DTO Generation

### 17. Design API DTOs without exposing entities

**Purpose:** design API DTOs without exposing entities.

**When to use it:** A REST endpoint needs stable request/response
contracts.

**Complete prompt:**

``` text
Design request and response DTOs for this Spring Boot 3.x API use case.

Use case:
[DESCRIBE OPERATION]
Current entity/domain types:
[PASTE RELEVANT TYPES]
Example JSON, if any:
[PASTE]

Requirements:
- Java 21; prefer records for immutable DTOs when appropriate.
- Explicit imports.
- Do not expose JPA entities directly.
- Separate request and response types when their responsibilities differ.
- Put Jakarta Validation constraints on inbound request DTOs where appropriate.
- Do not copy database-only fields into the public contract.
- Make nullability/optional fields explicit.
- Preserve API evolution concerns: names, identifiers, timestamps, enums, and compatibility.
- Avoid a generic BaseDto hierarchy.

First propose the contract and explain field choices. Then generate DTOs and focused validation/serialization tests.
Compile and run tests.
```

**Expected AI output:** Stable, minimal API contracts separated from
persistence entities.

**Optional follow-up prompts:** -
`Add an update DTO with explicit patch semantics.` -
`Review enum evolution and unknown-value handling for this contract.`

**Notes / cautions:** DTOs are API contracts, not mechanical copies of
entity fields.

### 18. Review DTO sprawl and duplication

**Purpose:** review DTO sprawl and duplication.

**When to use it:** A service has many nearly identical DTOs and mapping
code.

**Complete prompt:**

``` text
Review this Spring Boot service's DTO model for useful versus harmful duplication.

DTOs and endpoint use cases:
[PASTE OR ATTACH]

Classify duplication as:
- Intentional contract separation.
- Safe to consolidate.
- Similar-looking but semantically different and should remain separate.

Evaluate request/response reuse, create/update semantics, internal versus external DTOs, records, validation annotations, serialization concerns, and versioning.
Do not merge DTOs merely to reduce class count.
Prefer explicit contracts when consolidation would couple unrelated endpoints.
Return a proposed DTO map showing which endpoint uses which type.
Only then propose small refactors and tests.
```

**Expected AI output:** A semantic DTO consolidation review rather than
a superficial DRY exercise.

**Optional follow-up prompts:** -
`Implement the safest consolidation only.` -
`Identify DTOs that are accidentally tied to JPA entities.`

**Notes / cautions:** Some duplication protects API boundaries and is
cheaper than coupling.

## MapStruct

### 19. Introduce MapStruct for repetitive mappings

**Purpose:** introduce MapStruct for repetitive mappings.

**When to use it:** Manual DTO/entity mapping has become repetitive
enough to justify generated mapping.

**Complete prompt:**

``` text
Evaluate and, if justified, introduce MapStruct into this Java 21 / Spring Boot 3.x service.

Current mappings:
[PASTE MAPPING CODE]

First determine whether MapStruct improves maintainability here. Consider mapping volume, custom logic, nested mappings, update semantics, and debugging cost.
If justified:
- Add Gradle dependencies/annotation processor with compatible versions.
- Use Spring component model.
- Use constructor injection where collaborators are needed.
- Make unmapped target handling strict enough to catch accidental field drift.
- Keep business rules out of mapping methods.
- Use explicit mappings where names or semantics differ.
- Do not silently map identifiers, audit fields, or security-sensitive fields.
- Add unit tests for non-trivial mappings.

Implement incrementally, compile generated sources, and run tests.
If MapStruct is not justified, say so and recommend the simpler alternative.
```

**Expected AI output:** A reasoned MapStruct adoption decision and, when
appropriate, a strict tested mapper.

**Optional follow-up prompts:** -
`Add a partial-update mapper that does not overwrite fields with null unintentionally.` -
`Review generated mapper behavior for nested entities.`

**Notes / cautions:** Generated mapping is valuable for structural
translation, not domain decisions.

### 20. Review MapStruct mapper correctness

**Purpose:** review MapStruct mapper correctness.

**When to use it:** Existing mappers may silently overwrite or expose
fields.

**Complete prompt:**

``` text
Review these MapStruct mappers for correctness and maintainability.

Inputs:
[PASTE MAPPERS, DTOs, ENTITIES]

Inspect:
- Unmapped fields and implicit same-name mappings.
- ID/audit/version field handling.
- NullValuePropertyMappingStrategy and update behavior.
- Nested entity creation.
- Collection replacement versus merge semantics.
- Enum conversions.
- Date/time conversions and timezone assumptions.
- Cyclic mapping risk.
- Business logic embedded in expressions/default methods.
- Spring injection configuration.

For every risky mapping show a concrete input that would produce the wrong result.
Recommend explicit mappings where ambiguity exists.
Do not change all mappers at once; prioritize by data-loss or security risk.
Add tests before fixing high-risk behavior.
```

**Expected AI output:** A failure-oriented mapper audit with regression
tests and incremental corrections.

**Optional follow-up prompts:** -
`Add a regression test for the highest-risk update mapping.` -
`Make unmapped target fields fail the build where appropriate.`

**Notes / cautions:** Update mappers are especially dangerous because
null/default semantics can cause data loss.

## Service Layer

### 21. Design a transactional application service

**Purpose:** design a transactional application service.

**When to use it:** A use case needs orchestration across repositories
and domain operations.

**Complete prompt:**

``` text
Design and implement a Spring Boot application service for this use case.

Use case:
[DESCRIBE BUSINESS OPERATION]
Relevant entities/repositories/clients:
[PASTE OR ATTACH]

Requirements:
- Java 21, Spring Boot 3.x, explicit imports.
- Constructor injection.
- Keep controller concerns out of the service.
- Define a clear transaction boundary.
- Load only data required for the use case.
- Enforce business invariants in the appropriate domain/application location.
- Distinguish expected business failures from infrastructure failures.
- Avoid calling remote services while holding a database transaction unless there is a justified consistency requirement.
- Do not create an interface solely for mocking or convention.
- Make side effects and ordering explicit.
- Design for idempotency if the operation can be retried.

First explain the orchestration and transaction decision. Then implement with unit tests and, where persistence matters, integration tests.
Run all relevant tests.
```

**Expected AI output:** A focused application service with explicit
orchestration, transaction semantics, and tests.

**Optional follow-up prompts:** -
`Add idempotency for duplicate requests.` -
`Review whether any logic belongs in the aggregate rather than the service.`

**Notes / cautions:** Service classes should coordinate use cases, not
become generic containers for all business logic.

### 22. Review an oversized service class

**Purpose:** review an oversized service class.

**When to use it:** A service class has accumulated many
responsibilities.

**Complete prompt:**

``` text
Review this Spring Boot service class and identify cohesive responsibilities before proposing refactoring.

Code:
[PASTE SERVICE AND KEY COLLABORATORS]

Analyze:
- Distinct use cases mixed together.
- Business rules versus orchestration.
- Transaction boundaries.
- Repository and external-client coupling.
- Hidden temporal ordering.
- Repeated validation/mapping.
- Methods that belong to domain objects or dedicated collaborators.
- Test complexity as a signal of poor cohesion.

Do not split the class by arbitrary line count or create one class per method.
Propose a target responsibility map and dependency direction.
Rank refactor steps so each step preserves behavior and can be committed independently.
Add characterization tests before moving risky logic.
Compile and run tests after every step.
```

**Expected AI output:** A behavior-preserving decomposition plan based
on cohesion rather than class size.

**Optional follow-up prompts:** -
`Implement only the first refactor step.` -
`Identify which tests should become characterization tests before restructuring.`

**Notes / cautions:** More classes are not automatically better; each
extracted type should have a coherent reason to change.

## REST Controllers

### 23. Generate a REST controller from an approved contract

**Purpose:** generate a REST controller from an approved contract.

**When to use it:** An API contract exists and needs a thin Spring MVC
controller.

**Complete prompt:**

``` text
Implement a Spring Boot 3.x REST controller for this approved API contract.

Contract:
[PASTE METHOD, PATH, REQUEST, RESPONSE, STATUS CODES, ERROR CASES]
Service API:
[PASTE SERVICE METHODS]

Requirements:
- Java 21, explicit imports, constructor injection.
- Keep the controller thin: HTTP translation, validation, authorization hooks, and delegation only.
- Use request/response DTOs, not JPA entities.
- Use appropriate ResponseEntity/status semantics only where needed.
- Do not catch broad Exception in controller methods.
- Use Jakarta Validation for request validation.
- Make URI/path/query parameter semantics explicit.
- Preserve idempotency semantics for PUT/DELETE and document POST behavior.
- Do not return 200 for every outcome.
- Add MockMvc tests covering happy path, validation, not-found/business errors, and status/content type.

Explain endpoint decisions, implement incrementally, then run tests.
```

**Expected AI output:** A thin, contract-driven controller with
meaningful HTTP semantics and MockMvc coverage.

**Optional follow-up prompts:** -
`Add conditional request/ETag support if appropriate for this resource.` -
`Review whether pagination metadata belongs in headers or response body.`

**Notes / cautions:** HTTP status codes are part of the API contract;
choose them deliberately.

### 24. Review REST API semantics

**Purpose:** review REST API semantics.

**When to use it:** Existing controllers work but may have inconsistent
HTTP design.

**Complete prompt:**

``` text
Review these Spring Boot REST controllers as a production API contract.

Controllers/DTOs:
[PASTE OR ATTACH]

Evaluate:
- Resource naming and URI consistency.
- HTTP method semantics and idempotency.
- Status codes.
- Request validation.
- Content types.
- Error representation consistency.
- Pagination/filter/sort semantics.
- Entity exposure.
- Controller business logic.
- Backward compatibility implications of proposed changes.
- Security-sensitive parameters or mass-assignment risk.

Separate findings into:
1. Bugs/contract violations that should be fixed.
2. Design improvements requiring API versioning or client coordination.
3. Stylistic preferences that are not worth changing.

Do not break clients for cosmetic consistency.
Provide contract tests for any behavior-changing fix.
```

**Expected AI output:** A compatibility-aware REST review that
distinguishes defects from optional redesign.

**Optional follow-up prompts:** -
`Create tests that freeze the current public contract before refactoring.` -
`Propose a versioned migration for the breaking API issues.`

**Notes / cautions:** An aesthetically cleaner API is not worth an
unplanned breaking change.

## Validation

### 25. Design validation across API and domain layers

**Purpose:** design validation across API and domain layers.

**When to use it:** A use case has both structural input checks and
business invariants.

**Complete prompt:**

``` text
Design validation for this Spring Boot use case without duplicating rules across layers.

Request DTO/domain/use case:
[PASTE]

Classify each rule as:
- Transport/input shape validation suitable for Jakarta Validation.
- Domain invariant that must hold regardless of entry point.
- Application/use-case rule requiring repositories or external state.
- Database constraint that should remain enforced by the database.

Implement each rule in the appropriate layer.
Avoid service methods full of manual null/string checks that Jakarta Validation can handle.
Do not put repository calls inside Bean Validation constraints unless there is a compelling, well-tested reason.
Define stable error codes/messages for client-relevant validation failures.
Add tests at the layer where each rule lives and run them.
```

**Expected AI output:** A layered validation design with each rule
enforced in the right place.

**Optional follow-up prompts:** -
`Add cross-field validation for the request DTO.` -
`Map domain validation failures to the API error model.`

**Notes / cautions:** Validation placement should reflect where the
truth of the rule lives.

### 26. Review custom Bean Validation annotations

**Purpose:** review custom Bean Validation annotations.

**When to use it:** Custom validators may be stateful, overly broad, or
performing I/O.

**Complete prompt:**

``` text
Review these Jakarta Bean Validation constraints and validators in a Spring Boot 3.x application.

Code:
[PASTE ANNOTATIONS, VALIDATORS, DTOs]

Check:
- Whether a built-in constraint would be clearer.
- Null-handling semantics.
- Thread safety and mutable validator state.
- Cross-field versus field-level placement.
- Dependency injection or database/network I/O inside validators.
- Error message stability and localization.
- Validation groups that make control flow hard to understand.
- Test coverage for boundary values.

Recommend removing custom constraints that do not earn their complexity.
For retained validators, add focused unit tests and make failure semantics explicit.
Do not change public error messages without identifying compatibility impact.
```

**Expected AI output:** A concise custom-validation audit with simpler
alternatives and boundary tests.

**Optional follow-up prompts:** -
`Replace the unnecessary custom validator with standard constraints.` -
`Add tests for null, empty, boundary, and malformed values.`

**Notes / cautions:** Validators should be deterministic and cheap;
remote/database validation usually belongs in the application layer.

## Transactions

### 27. Review transaction boundaries

**Purpose:** review transaction boundaries.

**When to use it:** A service has @Transactional annotations but
consistency behavior is unclear.

**Complete prompt:**

``` text
Review transaction boundaries in this Spring Boot 3.x service.

Code:
[PASTE SERVICES, REPOSITORIES, AND EXTERNAL CLIENT CALLS]

For each use case determine:
- Where the transaction begins and ends.
- Which entities are managed.
- Whether lazy loading depends on an accidental open session.
- Whether remote calls occur inside database transactions.
- Whether readOnly=true is useful and correctly placed.
- Whether propagation settings are necessary or cargo cult.
- Whether checked/unchecked exception behavior affects rollback.
- Whether self-invocation makes @Transactional ineffective.
- Whether transaction length creates locking/contention risk.

Draw a concise call-path/transaction map.
Identify correctness issues before performance issues.
Recommend the smallest boundary changes and add integration tests that prove commit/rollback behavior.
Do not add REQUIRES_NEW unless the independent commit semantics are explicitly required.
```

**Expected AI output:** A transaction map, concrete failure analysis,
and test-backed boundary corrections.

**Optional follow-up prompts:** -
`Add a rollback integration test for the failure path.` -
`Move the remote call outside the transaction and explain the consistency tradeoff.`

**Notes / cautions:** Transaction annotations are proxies with specific
semantics; do not reason about them as ordinary method modifiers.

### 28. Design transaction handling for database plus messaging

**Purpose:** design transaction handling for database plus messaging.

**When to use it:** A use case writes database state and publishes an
event/message.

**Complete prompt:**

``` text
Design reliable transaction handling for this Spring Boot use case that updates a relational database and publishes a message.

Use case and infrastructure:
[DESCRIBE DB, BROKER, DELIVERY REQUIREMENTS]

Do not assume a distributed XA transaction.
Compare at least:
- Direct publish inside/after the database transaction.
- Transactional outbox.
- Broker-specific transaction support if applicable.

Evaluate failure windows, duplicate delivery, ordering, retry, idempotent consumers, and operational complexity.
Recommend the simplest design that satisfies these stated consistency requirements:
[REQUIREMENTS]

If recommending an outbox, define table/entity ownership, write transaction, publisher behavior, retry/locking approach, cleanup, and consumer idempotency.
Provide an incremental implementation plan and tests for the important failure windows.
```

**Expected AI output:** A consistency-driven database-plus-messaging
design with explicit failure semantics.

**Optional follow-up prompts:** -
`Implement the outbox write path first, without the publisher.` -
`Design the idempotent consumer contract for duplicate events.`

**Notes / cautions:** Exactly-once end-to-end delivery is usually not
available; design explicitly for retries and duplicates.

## Exception Handling

### 29. Create a consistent REST error model

**Purpose:** create a consistent REST error model.

**When to use it:** An API needs centralized, stable error responses.

**Complete prompt:**

``` text
Design and implement centralized exception handling for this Spring Boot 3.x REST API.

Known failure types:
[LIST DOMAIN, VALIDATION, NOT-FOUND, CONFLICT, INFRASTRUCTURE FAILURES]

Requirements:
- Use @RestControllerAdvice.
- Produce a stable error response with fields justified by client needs.
- Map expected domain/application failures to deliberate HTTP statuses.
- Handle Jakarta Validation errors consistently.
- Do not expose stack traces, SQL, class names, secrets, or internal exception messages to clients.
- Generate/propagate a correlation or trace identifier if the platform provides one.
- Log unexpected failures once at the appropriate boundary.
- Preserve the original exception as the cause when translating internally.
- Avoid a giant catch-all hierarchy of custom exceptions.

First propose the error taxonomy and mapping table. Then implement handlers and MockMvc tests.
Run tests.
```

**Expected AI output:** A centralized, non-leaky API error contract with
explicit mappings and tests.

**Optional follow-up prompts:** -
`Add RFC 9457 Problem Details if it fits the existing API contract.` -
`Review existing clients before changing error field names.`

**Notes / cautions:** Error payloads are public contracts; avoid leaking
implementation details into them.

### 30. Review exception translation and logging

**Purpose:** review exception translation and logging.

**When to use it:** A codebase catches and rethrows exceptions at many
layers.

**Complete prompt:**

``` text
Review exception handling across this Spring Boot call path.

Code:
[PASTE CONTROLLER -> SERVICE -> REPOSITORY/CLIENT PATH]

Identify:
- Catch-and-rethrow blocks that add no value.
- Lost causes/stack traces.
- Exceptions logged multiple times.
- Infrastructure exceptions leaking into API/domain contracts.
- Broad catches that hide programming errors.
- Retryable versus non-retryable failures.
- Incorrect conversion of every failure to 500 or 400.
- Transaction rollback implications.

Propose a small exception taxonomy based on caller decisions, not one exception class per failure message.
State exactly where each failure should be translated and where it should be logged.
Add tests for the most important translations before refactoring.
```

**Expected AI output:** A boundary-oriented exception strategy that
reduces noise and preserves diagnostics.

**Optional follow-up prompts:** -
`Remove redundant catch/log/rethrow blocks incrementally.` -
`Add a test proving the original cause is preserved.`

**Notes / cautions:** Translate exceptions when crossing a meaningful
abstraction boundary, not at every method.

## Logging

### 31. Design production logging for a Spring Boot service

**Purpose:** design production logging for a Spring Boot service.

**When to use it:** A service needs useful logs without leaking data or
generating noise.

**Complete prompt:**

``` text
Design a production logging approach for this Spring Boot 3.x service.

Context:
[DESCRIBE REQUEST FLOW, DEPLOYMENT, OBSERVABILITY STACK]

Define:
- What should be logged at application boundaries.
- Appropriate ERROR/WARN/INFO/DEBUG usage.
- Correlation/trace identifiers and how they enter log context.
- Structured logging fields if the platform supports them.
- Which identifiers are safe and operationally useful.
- Sensitive data that must never be logged.
- Exception logging rules to avoid duplicate stack traces.
- Logging around retries, timeouts, and external calls.
- Configuration differences between local development and production.

Do not add logs to every method.
Prefer telemetry/metrics for high-volume numeric events.
Provide representative code/config changes and tests where log behavior is security-sensitive.
Keep changes incremental.
```

**Expected AI output:** A low-noise, traceable logging design aligned
with production operations.

**Optional follow-up prompts:** -
`Add structured JSON logging compatible with the stated platform.` -
`Review the codebase for accidental credential or PII logging.`

**Notes / cautions:** Logs should support diagnosis; they should not
become an unbounded event database.

### 32. Review logs for sensitive-data exposure

**Purpose:** review logs for sensitive-data exposure.

**When to use it:** A security or production-readiness review needs to
inspect logging.

**Complete prompt:**

``` text
Perform a sensitive-data logging review of this Spring Boot code and configuration.

Inputs:
[PASTE OR ATTACH CONTROLLERS, SERVICES, CLIENTS, EXCEPTION HANDLERS, LOG CONFIG]

Look for:
- Passwords, tokens, Authorization headers, cookies, API keys.
- Full request/response bodies.
- Personal or regulated data.
- Database connection strings.
- Secrets embedded in exception messages.
- DTO/entity toString() output.
- Logging interceptors/filters that capture headers or bodies.
- DEBUG/TRACE settings unsafe for production.

For each issue identify the exact log statement/configuration and a safer replacement.
Prefer allow-listing safe fields over brittle redaction after serialization.
Do not remove operationally useful identifiers unnecessarily.
Add a regression test or static check for the highest-risk case where practical.
```

**Expected AI output:** A concrete log-leakage audit with safer
field-level logging recommendations.

**Optional follow-up prompts:** -
`Implement safe request logging with an allow-list.` -
`Review exception handlers for indirect secret leakage.`

**Notes / cautions:** Redaction is a fallback; avoiding collection of
sensitive values is safer.

## Testing

### 33. Generate a focused service unit test suite

**Purpose:** generate a focused service unit test suite.

**When to use it:** A service class needs fast behavioral tests without
over-mocking implementation details.

**Complete prompt:**

``` text
Create JUnit Jupiter tests for this Spring Boot service class.

Code:
[PASTE SERVICE AND COLLABORATOR INTERFACES/CLASSES]

Use Mockito only for true external collaborators.
Requirements:
- Java 21 with explicit imports; no wildcard imports.
- Arrange / Act / Assert structure where it improves readability.
- Test observable behavior and important collaborator interactions, not private implementation.
- Cover happy path, business rejection, and meaningful edge cases.
- Do not mock value objects or simple domain objects.
- Avoid lenient stubbing.
- Verify interactions only when the interaction itself is part of the behavior.
- Use descriptive test method names.
- Do not start a Spring context for a pure unit test.

After writing tests, run the relevant Gradle test task and fix compilation/test failures.
Report any production-code design issue that makes testing unnecessarily difficult rather than hiding it with complicated mocks.
```

**Expected AI output:** A fast, readable unit suite that tests behavior
with minimal mocking.

**Optional follow-up prompts:** -
`Add a parameterized test for the boundary cases.` -
`Refactor the production code only if the tests expose a real cohesion problem.`

**Notes / cautions:** Mocking every collaborator interaction creates
brittle tests that mirror implementation.

### 34. Create Testcontainers persistence integration tests

**Purpose:** create Testcontainers persistence integration tests.

**When to use it:** Repository/query behavior must be verified against
the production database engine.

**Complete prompt:**

``` text
Create Spring Boot 3.x integration tests for these Spring Data JPA repositories using Testcontainers.

Database engine/version: [POSTGRESQL/MYSQL/etc.]
Repositories/entities: [PASTE OR ATTACH]
Queries to verify: [LIST]

Requirements:
- Java 21, Gradle, explicit imports.
- Use a real containerized database compatible with production.
- Apply the same Flyway/Liquibase migrations used by the application if present.
- Keep test data small and explicit.
- Verify query semantics, constraints, mappings, transaction behavior, and ordering/pagination where relevant.
- Do not replace the database with H2 when dialect behavior matters.
- Make tests deterministic and independent.
- Reuse container lifecycle sensibly without sharing mutable test state.

Run the integration-test task and report container/database versions and results.
```

**Expected AI output:** Database-realistic integration tests that catch
mapping and SQL behavior issues.

**Optional follow-up prompts:** -
`Add a test for the unique/foreign-key constraint failure.` -
`Capture and review generated SQL for the slowest query.`

**Notes / cautions:** An in-memory database can mask dialect, type, and
constraint differences.

## Refactoring

### 35. Plan a behavior-preserving Spring Boot refactor

**Purpose:** plan a behavior-preserving Spring Boot refactor.

**When to use it:** Production code needs restructuring without feature
changes.

**Complete prompt:**

``` text
Plan a behavior-preserving refactor of this Spring Boot code.

Code:
[PASTE OR ATTACH]
Pain points:
[DESCRIBE]

Before modifying anything:
1. Identify current responsibilities and externally observable behavior.
2. Identify tests that already protect that behavior.
3. List missing characterization tests needed before refactoring.
4. Propose small, independently reviewable steps.
5. Separate structural changes from behavioral changes.

Constraints:
- Java 21, Spring Boot 3.x.
- Preserve public API and persistence behavior unless explicitly approved.
- Prefer constructor injection and explicit dependencies.
- Do not introduce patterns/interfaces solely for aesthetic purity.
- Do not rewrite the entire package.
- Compile and run tests after each step.
- Stop and report if a step reveals an assumption that tests do not cover.

Return the plan first. Do not edit code until I approve the first step.
```

**Expected AI output:** A low-risk refactoring sequence protected by
characterization tests.

**Optional follow-up prompts:** -
`Implement step 1 only and show the diff rationale.` -
`After step 1, reassess whether the remaining steps are still justified.`

**Notes / cautions:** Refactoring risk rises sharply when structural and
behavioral changes are mixed.

### 36. Replace accidental complexity with standard Spring patterns

**Purpose:** replace accidental complexity with standard Spring
patterns.

**When to use it:** Custom infrastructure may duplicate Spring Boot
capabilities.

**Complete prompt:**

``` text
Review this custom Spring/Spring Boot infrastructure and determine whether standard framework facilities can replace it.

Code:
[PASTE CUSTOM FACTORIES, REGISTRIES, CONFIGURATION, INTERCEPTORS, WRAPPERS]

For each custom abstraction answer:
- What problem does it solve?
- Does Spring Boot 3.x already solve that problem?
- What behavior would be lost by removing it?
- Is the custom code easier to test and operate than the standard mechanism?
- Does it hide lifecycle, transaction, proxy, or configuration behavior?

Do not replace working custom code merely because a Spring annotation exists.
Recommend removal only when the standard facility is materially simpler and behaviorally equivalent.
Create characterization tests before replacing infrastructure.
Make one replacement at a time and run the full test suite.
```

**Expected AI output:** A measured simplification plan that removes only
unjustified custom infrastructure.

**Optional follow-up prompts:** -
`Replace the highest-value custom abstraction only.` -
`Document the Spring lifecycle/proxy behavior the replacement relies on.`

**Notes / cautions:** Framework conventions reduce maintenance only when
engineers can still reason clearly about runtime behavior.

## Performance

### 37. Diagnose a slow Spring Boot endpoint

**Purpose:** diagnose a slow Spring Boot endpoint.

**When to use it:** An endpoint is slow and needs evidence-based
analysis.

**Complete prompt:**

``` text
Diagnose this slow Spring Boot endpoint as a performance engineer. Do not optimize by intuition alone.

Endpoint/use case:
[DESCRIBE]
Measurements/traces/logs:
[PASTE]
Relevant controller/service/repository/client code:
[PASTE OR ATTACH]

Build a latency breakdown across:
- Request parsing/filter chain.
- Application/service work.
- Database queries and connection acquisition.
- External HTTP/messaging calls.
- Serialization.
- Thread/connection pool waits.
- GC/CPU symptoms if evidence exists.

Identify what evidence is missing and suggest the cheapest measurement to obtain it.
Inspect generated SQL/query count for N+1 and unnecessary data loading.
Do not recommend caching, async execution, virtual threads, pool increases, or indexes until the bottleneck supports them.
Rank optimizations by expected impact, risk, and measurement required.
Implement only the approved highest-value change, then rerun the same measurement and tests.
```

**Expected AI output:** A measurement-driven latency diagnosis with
prioritized, verifiable improvements.

**Optional follow-up prompts:** -
`Instrument the endpoint with Micrometer timers around the suspected bottleneck.` -
`Review the repository query plan and index coverage.`

**Notes / cautions:** Performance work should compare before/after
measurements under comparable conditions.

### 38. Review connection and thread pool configuration

**Purpose:** review connection and thread pool configuration.

**When to use it:** A service shows saturation, timeouts, or uncertain
concurrency tuning.

**Complete prompt:**

``` text
Review concurrency-related pool configuration for this Spring Boot 3.x service.

Inputs:
[APPLICATION CONFIG, DEPLOYMENT CPU/MEMORY, TRAFFIC, LATENCY, DB LIMITS, METRICS]

Evaluate together:
- HTTP request concurrency.
- Platform versus virtual thread model if configured.
- HikariCP maximum pool size and acquisition timeout.
- Database max connections and replica topology.
- Outbound HTTP client connection pools/timeouts.
- Executor pools used by @Async or custom work.
- Kubernetes replica count/HPA if applicable.

Explain queueing/backpressure interactions; do not tune each pool independently.
Do not use formulas without stating assumptions.
Recommend values only where workload data supports them; otherwise specify the measurements needed.
Highlight configurations that can multiply connections unexpectedly as replicas scale.
Provide a load-test validation plan before production rollout.
```

**Expected AI output:** A system-level pool analysis that prevents local
tuning from overloading downstream resources.

**Optional follow-up prompts:** -
`Calculate database connection demand across the expected replica range.` -
`Evaluate whether Java 21 virtual threads change the bottleneck in this workload.`

**Notes / cautions:** Virtual threads reduce thread scarcity; they do
not create more database connections or downstream capacity.

## Security

### 39. Review Spring Security configuration

**Purpose:** review Spring Security configuration.

**When to use it:** A resource server or web application needs a focused
authorization review.

**Complete prompt:**

``` text
Review this Spring Boot 3.x / Spring Security 6 configuration as a senior application-security engineer.

Security configuration:
[PASTE]
Authentication model:
[OIDC/OAUTH2 RESOURCE SERVER/SESSION/etc.]
Authorization requirements:
[PASTE]

Check:
- Authentication versus authorization responsibilities.
- Endpoint matcher ordering and unintended permitAll paths.
- CSRF handling appropriate to the client model.
- CORS configuration.
- JWT issuer/audience/signature validation.
- Scope/authority/role mapping.
- Method security consistency.
- Stateless/session configuration.
- Error handling that leaks details.
- Actuator and documentation endpoint exposure.
- Security headers where relevant.
- Tests for allowed and denied access.

Do not weaken controls to make tests pass.
Explain every recommended rule in terms of a threat or requirement.
Provide minimal changes and security-focused MockMvc tests.
```

**Expected AI output:** A threat-linked Spring Security review with
explicit authorization tests.

**Optional follow-up prompts:** -
`Add audience validation for access tokens.` -
`Create tests proving anonymous, insufficient-scope, and authorized behavior.`

**Notes / cautions:** Authentication success does not imply
authorization; test both.

### 40. Threat-model a Spring Boot REST endpoint

**Purpose:** threat-model a Spring Boot REST endpoint.

**When to use it:** A sensitive endpoint needs design review before
implementation.

**Complete prompt:**

``` text
Threat-model this Spring Boot REST operation before code is changed.

Operation:
[DESCRIBE ENDPOINT, ACTOR, DATA, SIDE EFFECTS]
Trust boundaries/integrations:
[DESCRIBE]

Analyze practical threats including:
- Broken object-level authorization.
- Broken function-level authorization.
- Mass assignment.
- Injection.
- Replay/idempotency issues.
- Sensitive-data exposure.
- Excessive data returned.
- Abuse/rate concerns.
- SSRF if user-controlled URLs exist.
- File/path risks if uploads/downloads exist.
- Audit requirements.

For each relevant threat provide:
1. Concrete attack scenario.
2. Required control and the layer that owns it.
3. Test that proves the control.
4. Residual risk.

Do not produce generic OWASP lists unrelated to this endpoint.
Then review the proposed implementation against the threat model.
```

**Expected AI output:** A use-case-specific threat model tied directly
to controls and executable tests.

**Optional follow-up prompts:** -
`Turn the authorization threats into MockMvc test cases.` -
`Review DTO fields for mass-assignment exposure.`

**Notes / cautions:** Security reviews are most useful when each threat
maps to a concrete control and verification.

## Production Readiness

### 41. Perform a production-readiness review

**Purpose:** perform a production-readiness review.

**When to use it:** A service is approaching first production
deployment.

**Complete prompt:**

``` text
Perform a production-readiness review of this Java 21 / Spring Boot 3.x service.

Inputs:
[ATTACH REPOSITORY, CONFIG, DEPLOYMENT MANIFESTS, RUNBOOK IF AVAILABLE]

Evaluate only evidence visible in the repository and clearly mark unknowns.
Cover:
- Build reproducibility and CI tests.
- Configuration/secrets.
- Health, readiness, liveness semantics.
- Graceful shutdown.
- Database migrations.
- Connection/timeouts/retries.
- Logging, metrics, tracing.
- Security and Actuator exposure.
- Resource limits and JVM/container settings.
- Failure behavior for unavailable dependencies.
- Backup/recovery assumptions where the service owns data.
- Deployment/rollback compatibility.
- Operational runbook gaps.

Return a release-blocker checklist grouped Critical/High/Medium/Low.
For each blocker include evidence, failure mode, and exact remediation.
Do not recommend tooling merely because it is fashionable.
Implement nothing until the blockers are reviewed.
```

**Expected AI output:** A repository-grounded go-live assessment with
prioritized blockers and remediation.

**Optional follow-up prompts:** -
`Convert the Critical/High findings into an implementation sequence.` -
`Create a minimal operational runbook from the approved architecture.`

**Notes / cautions:** A readiness review should distinguish repository
evidence from deployment assumptions.

### 42. Design Actuator health and readiness semantics

**Purpose:** design Actuator health and readiness semantics.

**When to use it:** Kubernetes or another orchestrator needs health
signals that reflect recoverability.

**Complete prompt:**

``` text
Design Spring Boot Actuator health semantics for this service.

Dependencies:
[DATABASE / BROKER / EXTERNAL APIs / CACHE / OTHER]
Deployment platform:
[KUBERNETES/ECS/etc.]

Define:
- Liveness: what proves the process must be restarted?
- Readiness: what conditions mean the instance should stop receiving traffic?
- Startup behavior if applicable.
- Which dependencies should NOT make liveness fail.
- Timeout behavior for custom health indicators.
- Whether a dependency outage should remove all replicas from service and create an outage loop.

Use Spring Boot 3.x Actuator facilities before creating custom indicators.
Expose only necessary endpoints and details.
Provide configuration, any required custom indicator code with explicit imports, and tests.
Explain orchestrator probe settings separately from application health logic.
```

**Expected AI output:** Correct liveness/readiness semantics that avoid
restart storms and false availability.

**Optional follow-up prompts:** -
`Adapt the probes to the provided Kubernetes Deployment.` -
`Add a readiness test for database-unavailable behavior.`

**Notes / cautions:** Liveness should usually detect unrecoverable
process health, not every downstream dependency outage.

## Architecture Reviews

### 43. Review Spring Boot service architecture

**Purpose:** review Spring Boot service architecture.

**When to use it:** A service needs a senior-level architecture
assessment before major changes.

**Complete prompt:**

``` text
Review this Spring Boot service as a senior Java architect.

Repository/design:
[ATTACH OR DESCRIBE]

Evaluate:
- Package/module boundaries and dependency direction.
- Controller/service/repository layering where it adds value.
- Domain model versus transaction-script style and whether that fits complexity.
- Transaction boundaries.
- Persistence coupling and JPA usage.
- DTO/API boundaries.
- External integration isolation.
- Configuration and dependency injection.
- Test strategy.
- Failure handling and observability.
- Security boundaries.
- Performance risks visible from design.

Do not score the code against a pattern checklist.
Distinguish actual problems from acceptable tradeoffs.
For every recommendation state: evidence, risk, proposed change, benefit, cost, and what not to change.
Prioritize the top five changes by engineering value.
Do not modify code until recommendations are approved.
```

**Expected AI output:** A pragmatic architecture review focused on
high-value changes rather than pattern compliance.

**Optional follow-up prompts:** -
`Turn the top recommendation into a sequence of small pull requests.` -
`Identify which current design choices should explicitly remain unchanged.`

**Notes / cautions:** Architecture review quality depends as much on
rejecting unnecessary change as proposing change.

### 44. Evaluate whether to split a Spring Boot service

**Purpose:** evaluate whether to split a Spring Boot service.

**When to use it:** A service may be too broad, but a microservice split
has significant cost.

**Complete prompt:**

``` text
Evaluate whether this Spring Boot application should remain one deployable service or be split.

Current responsibilities:
[DESCRIBE]
Data ownership:
[DESCRIBE]
Team/deployment constraints:
[DESCRIBE]
Pain points:
[DESCRIBE]

Analyze:
- Cohesion and change coupling.
- Transactional consistency needs.
- Independent scaling/deployment evidence.
- Data ownership boundaries.
- Failure isolation.
- Team ownership.
- Operational complexity introduced by a split.
- API/event contracts and migration sequencing.

Do not recommend microservices merely because the codebase is large.
Consider a modular monolith/package boundary as an explicit alternative.
If a split is justified, identify one candidate boundary and a strangler-style migration plan that preserves behavior.
Include rollback and data-migration risks.
```

**Expected AI output:** A cost-aware service-boundary decision with a
modular alternative and incremental migration path.

**Optional follow-up prompts:** -
`Design module boundaries before considering a deployment split.` -
`Define the data ownership and API contract for the candidate extracted service.`

**Notes / cautions:** A distributed system should be introduced only
when its operational cost buys a concrete capability.

## Code Reviews

### 45. Perform a senior Spring Boot pull-request review

**Purpose:** perform a senior Spring Boot pull-request review.

**When to use it:** A PR needs a rigorous, actionable review.

**Complete prompt:**

``` text
Review this Spring Boot pull request as a senior Java engineer.

Diff/context:
[PASTE DIFF OR ATTACH CHANGED FILES]
Intended behavior:
[DESCRIBE]

Review for:
- Correctness and edge cases.
- Java 21 idioms without cleverness.
- Spring Boot/Spring lifecycle and proxy behavior.
- Constructor injection and dependency design.
- Transaction boundaries.
- JPA/query behavior.
- API compatibility.
- Validation and exception handling.
- Security.
- Concurrency/thread safety.
- Performance regressions.
- Test quality and missing tests.
- Operational/configuration impact.

Only report issues that are actionable and supported by the code.
Classify as Blocker, Major, Minor, or Question.
For each issue cite the relevant code, explain the failure scenario, and propose the smallest fix.
Do not rewrite unrelated code or flood the review with style preferences.
```

**Expected AI output:** A concise, severity-ranked PR review tied to
concrete failure scenarios.

**Optional follow-up prompts:** -
`Review only the transaction and JPA implications in more depth.` -
`Propose tests that would reproduce each Blocker/Major issue.`

**Notes / cautions:** High-signal reviews prioritize correctness and
maintainability over personal style.

### 46. Review AI-generated Spring Boot code before merge

**Purpose:** review AI-generated Spring Boot code before merge.

**When to use it:** Code generated by an AI assistant needs verification
for plausible-but-wrong framework usage.

**Complete prompt:**

``` text
Audit this AI-generated Spring Boot 3.x code before it is merged.

Generated code:
[PASTE OR ATTACH]
Requested behavior:
[PASTE ORIGINAL REQUIREMENT]

Assume the code may be syntactically plausible but semantically wrong.
Verify:
- Imports/APIs actually exist for Spring Boot 3.x and Java 21.
- Dependency requirements are present in Gradle.
- Annotations have the intended runtime effect.
- Proxy limitations such as self-invocation.
- JPA ownership/fetch/cascade semantics.
- Transaction rollback behavior.
- Security defaults.
- Serialization and validation.
- Null/error paths.
- Tests assert behavior rather than merely execute code.

Compile and run tests rather than claiming the code works.
Add the smallest missing tests that can falsify risky assumptions.
Return verified issues and exact evidence; do not speculate when a quick build/test can decide.
```

**Expected AI output:** A verification-oriented review that catches
hallucinated APIs and framework semantic errors.

**Optional follow-up prompts:** -
`Run dependencyInsight for any newly introduced library.` -
`Add a regression test for the riskiest framework assumption.`

**Notes / cautions:** AI-generated framework code should be treated like
an untrusted patch until compiled and behaviorally tested.

## Debugging

### 47. Debug a Spring Boot startup failure

**Purpose:** debug a Spring Boot startup failure.

**When to use it:** The application fails during context initialization.

**Complete prompt:**

``` text
Diagnose this Spring Boot 3.x startup failure from evidence, not generic guesses.

Startup log/stack trace:
[PASTE FULL RELEVANT TRACE]
Recent changes:
[DESCRIBE]
Configuration/build files:
[PASTE IF RELEVANT]

Work from the deepest meaningful cause outward.
Determine whether the failure is primarily:
- Configuration binding.
- Bean creation/dependency cycle.
- Missing/ambiguous bean.
- Classpath/dependency incompatibility.
- Database/migration.
- Security configuration.
- Port/network/resource.
- Application code.

Explain the bean/configuration chain that leads to the failure.
Identify one minimal experiment or change that proves the diagnosis.
Do not suggest deleting caches or rebuilding everything unless evidence supports it.
After the fix, run the application/tests and verify the original failure is gone.
```

**Expected AI output:** A root-cause startup diagnosis with a
falsifiable minimal fix.

**Optional follow-up prompts:** -
`Explain why Spring attempted to create the failing bean.` -
`Add a context-load test that prevents this regression.`

**Notes / cautions:** The first exception line is often not the root
cause; follow nested causes carefully.

### 48. Debug an intermittent production error

**Purpose:** debug an intermittent production error.

**When to use it:** A Spring Boot service fails only under certain
production conditions.

**Complete prompt:**

``` text
Investigate this intermittent Spring Boot production failure.

Symptoms:
[ERRORS, FREQUENCY, IMPACT]
Logs/traces/metrics:
[PASTE]
Relevant code:
[PASTE]
Environment/deployment:
[DESCRIBE]

Build competing hypotheses covering only evidence-supported areas such as concurrency, pool exhaustion, timeouts, retries, stale data, transaction isolation, external dependency behavior, or resource pressure.
For each hypothesis provide:
- Evidence for and against.
- Additional telemetry/query needed.
- A safe reproduction or load-test strategy.
- A discriminating test that would rule it in or out.

Do not make code changes until the likely cause is narrowed.
Avoid increasing timeouts/pools as a first response.
Once evidence identifies the cause, propose the smallest remediation and a regression/soak test.
```

**Expected AI output:** A hypothesis-driven incident analysis that
narrows intermittent failures before changing production code.

**Optional follow-up prompts:** -
`Design the telemetry needed to distinguish the top two hypotheses.` -
`Create a targeted concurrency/load test for the leading hypothesis.`

**Notes / cautions:** Intermittent failures are easy to mask with
retries or larger pools; prove the bottleneck first.

## Build Failures

### 49. Diagnose a compile failure after a Spring Boot upgrade

**Purpose:** diagnose a compile failure after a Spring Boot upgrade.

**When to use it:** A framework or Java upgrade breaks compilation.

**Complete prompt:**

``` text
Diagnose these compilation failures after upgrading to Java 21 / Spring Boot 3.x.

Build output:
[PASTE COMPLETE RELEVANT ERRORS]
Old/new versions:
[PASTE]
Changed build files:
[PASTE]

Group errors by root cause rather than fixing files one by one.
Check:
- javax.* to jakarta.* namespace changes.
- Removed/deprecated Spring APIs.
- Security 6 configuration changes.
- Hibernate/JPA API changes.
- Annotation processor compatibility.
- Test API changes.
- Dependency version mismatches.

For each root cause identify the official replacement concept and the smallest migration.
Do not suppress deprecation/errors with unsafe casts or exclusions.
Apply one root-cause fix at a time, rerun ./gradlew clean test, and use the new error set to guide the next step.
Report final build/test status.
```

**Expected AI output:** A root-cause-oriented upgrade repair that avoids
whack-a-mole compilation fixes.

**Optional follow-up prompts:** -
`Handle only the Jakarta namespace failures first.` -
`After compilation succeeds, review runtime migration risks not caught by the compiler.`

**Notes / cautions:** Compiler success after an upgrade does not prove
behavioral compatibility; follow with focused integration tests.

### 50. Diagnose a test that passes locally but fails in CI

**Purpose:** diagnose a test that passes locally but fails in CI.

**When to use it:** A Spring Boot test is environment-dependent or
flaky.

**Complete prompt:**

``` text
Diagnose why this Spring Boot/Gradle test passes locally but fails in CI.

CI failure:
[PASTE LOG]
Test/code:
[PASTE]
Local and CI environment differences:
[PASTE JAVA/GRADLE/OS/DB/TIMEZONE/LOCALE/PARALLELISM IF KNOWN]

Investigate deterministic causes first:
- Test ordering/shared state.
- Timezone/locale/clock assumptions.
- Port/file-system/path assumptions.
- Parallel execution.
- External network dependency.
- Database engine/version.
- Missing environment variables.
- Race conditions/timeouts.
- Gradle daemon/cache differences.

Do not solve flakiness by adding sleeps or retries unless retry behavior is itself under test.
Create a local reproduction strategy matching CI.
Fix the underlying isolation/assumption and run the failing test repeatedly plus the full suite.
```

**Expected AI output:** A reproducible CI-failure diagnosis and
deterministic test fix.

**Optional follow-up prompts:** -
`Make the test independent of system time using an injected Clock.` -
`Run the test repeatedly with Gradle parallel execution enabled.`

**Notes / cautions:** Flaky tests are production signals when they
expose hidden timing or shared-state assumptions.

## Deployment

### 51. Create a production Dockerfile for Spring Boot

**Purpose:** create a production Dockerfile for Spring Boot.

**When to use it:** A service needs a container image suitable for
production deployment.

**Complete prompt:**

``` text
Create/review a production Dockerfile for this Java 21 / Spring Boot 3.x Gradle service.

Repository/build:
[ATTACH OR DESCRIBE]
Runtime platform:
[KUBERNETES/ECS/etc.]

Requirements:
- Use a supported Java 21 runtime image appropriate to organizational policy.
- Build artifact in CI or use a justified multi-stage build; explain the choice.
- Run as a non-root user.
- Keep image contents minimal without obscure tricks.
- Preserve container signal handling and graceful Spring shutdown.
- Do not bake secrets or environment-specific config into the image.
- Expose/document the application port without assuming EXPOSE publishes it.
- Set JVM/container options only when justified.
- Include a .dockerignore.
- Make image reproducible and scan-friendly.

Build the image, run it, exercise the health endpoint, and run the project tests where tooling permits.
Explain each non-obvious Docker decision.
```

**Expected AI output:** A maintainable, non-root Spring Boot image with
verified startup/health behavior.

**Optional follow-up prompts:** -
`Add OCI labels and a version/build metadata strategy.` -
`Review JVM memory behavior against the container memory limit.`

**Notes / cautions:** Do not over-optimize image layers at the cost of
clarity and reproducibility.

### 52. Review Kubernetes deployment settings for Spring Boot

**Purpose:** review Kubernetes deployment settings for Spring Boot.

**When to use it:** A Spring Boot workload is being deployed to
Kubernetes/EKS.

**Complete prompt:**

``` text
Review this Kubernetes Deployment/Service configuration for a Spring Boot 3.x application.

Manifests/Helm values:
[PASTE OR ATTACH]
Application health/config:
[PASTE]
Traffic/resource information:
[DESCRIBE]

Evaluate:
- Container port and Service targetPort consistency.
- Readiness/liveness/startup probes and Actuator semantics.
- CPU/memory requests and limits.
- JVM memory behavior under the limit.
- Graceful termination, preStop if needed, and terminationGracePeriodSeconds.
- Rolling update settings and capacity during deployment.
- ConfigMap/Secret injection.
- Service account/IAM permissions.
- HPA assumptions.
- Pod disruption and availability needs.
- Database connection multiplication as replicas scale.

Do not invent resource numbers without workload data.
Separate application changes from platform-manifest changes.
Return release blockers first, then a minimal patch.
Validate manifests and run application tests after code changes.
```

**Expected AI output:** A Spring-aware Kubernetes deployment review that
connects probe, shutdown, resource, and scaling behavior.

**Optional follow-up prompts:** -
`Calculate connection-pool demand at maximum HPA replicas.` -
`Review rolling-update settings for zero-downtime compatibility.`

**Notes / cautions:** Application and orchestrator settings form one
runtime system; review them together.

## General Best Practices

### 53. Establish Spring Boot engineering conventions for a repository

**Purpose:** establish Spring Boot engineering conventions for a
repository.

**When to use it:** A team needs concise, enforceable conventions for
ongoing AI-assisted development.

**Complete prompt:**

``` text
Create a repository-specific Spring Boot engineering conventions document from the codebase, not a generic style guide.

Repository:
[ATTACH]
Known constraints:
- Java 21.
- Spring Boot 3.x.
- Gradle.
- Jakarta Persistence where JPA is used.
- Constructor injection.
- Maintainability over cleverness.

Infer existing good conventions before proposing new ones.
Cover only conventions that materially improve consistency:
- Package/module boundaries.
- Dependency injection.
- DTO/entity separation.
- Transaction placement.
- Repository/query practices.
- Validation.
- Exception/error handling.
- Testing levels.
- Configuration.
- Logging/observability.
- Security.
- Build/dependency management.

For each convention include rationale and one short good/bad example where useful.
Avoid rules that are merely personal formatting preferences or already enforced automatically.
Mark proposed changes that would require team agreement.
```

**Expected AI output:** A concise, repository-grounded engineering
standard suitable for CONTRIBUTING.md or AI instructions.

**Optional follow-up prompts:** -
`Convert the approved conventions into a CODE_REVIEW_CHECKLIST.md.` -
`Create concise instructions for Codex/AI agents from these conventions.`

**Notes / cautions:** Standards should reduce recurring decisions; too
many low-value rules make important rules invisible.

### 54. Perform a maintainability-first final review

**Purpose:** perform a maintainability-first final review.

**When to use it:** A feature is complete and needs a final holistic
review before merge.

**Complete prompt:**

``` text
Perform a final maintainability-first review of this completed Spring Boot feature.

Requirement:
[PASTE]
Changed code/diff:
[PASTE OR ATTACH]

Verify:
- The implementation satisfies the requirement without unrelated scope.
- Names and responsibilities are clear.
- Dependencies flow in understandable directions.
- Spring/JPA/transaction semantics are correct.
- API and persistence contracts are intentional.
- Error and validation paths are covered.
- Security assumptions are explicit.
- Performance risks are proportional to expected workload.
- Tests protect behavior and important integration points.
- Configuration/operations changes are documented.
- No speculative abstractions, dead code, or unnecessary dependencies were introduced.

Compile and run all relevant tests.
Return only:
1. Merge blockers.
2. Important non-blocking improvements.
3. Things that are deliberately good enough and should not be changed.
4. Build/test evidence.
Do not propose a rewrite if the feature is already maintainable.
```

**Expected AI output:** A high-signal pre-merge assessment that includes
an explicit 'do not change' category.

**Optional follow-up prompts:** -
`Fix only the merge blockers and rerun tests.` -
`Produce a concise PR description explaining architectural decisions and verification.`

**Notes / cautions:** Good engineering review includes recognizing when
further refactoring has negative return.
