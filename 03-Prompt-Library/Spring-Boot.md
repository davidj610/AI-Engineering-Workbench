# Spring Boot

> **AI Engineering Workbench — Version 1**

Production-quality, context-aware prompts for experienced engineers working with Java 21, Spring Boot 3.x, Gradle, and Jakarta APIs. Each prompt assumes the AI engineering agent already has access to the current repository, workspace, project, selected resource, build output, and other context available in the active engineering environment.

---

## Project Creation

### 1. Create a production Spring Boot service

**Purpose:** Create a production Spring Boot service.

**When to use it:** Starting a new production service or extracting a service from a larger system.

**Complete prompt:**

```text
Act as a senior Java architect. Create the initial implementation for a production Spring Boot 3.x service using Java 21 and Gradle.

First inspect the existing repository and follow its conventions unless they conflict with the requirements below. Make incremental changes rather than replacing existing project structure unnecessarily.

Requirements:
- Use Java 21.
- Use the repository's current Spring Boot 3.x line; do not upgrade versions unless necessary and explain any version change.
- Use Gradle and preserve the existing Gradle DSL if one exists.
- Prefer constructor injection.
- Use Jakarta APIs, including Jakarta Persistence where persistence is required.
- Establish clear package boundaries for API, application/service, domain, and infrastructure concerns without creating ceremonial layers that add no value.
- Add only dependencies that are justified by an immediate requirement.
- Externalize environment-specific configuration.
- Include health/readiness support appropriate to the deployment environment.
- Add a minimal test strategy: fast unit tests plus focused Spring integration tests where framework behavior matters.
- Do not add Lombok unless the repository already uses it or there is a compelling project-specific reason.

Before editing, summarize the proposed structure and important architectural decisions. Then implement the smallest coherent baseline. Compile the project and run the relevant tests. Fix failures caused by your changes.

Return:
1. Files created or changed.
2. Key architectural decisions and rationale.
3. Build/test commands executed and results.
4. Remaining production concerns or deliberate deferrals.
```

**Expected AI output:** A minimal, compiling service baseline with justified structure, tests, configuration, and a concise decision log.

**Optional follow-up prompts:**

- `Add the first vertical feature slice without changing the approved architecture.`
- `Review the generated baseline for unnecessary framework or dependency complexity.`

**Notes or cautions:** Do not introduce speculative infrastructure, abstractions, or dependencies.

---

### 2. Add a feature as a vertical slice

**Purpose:** Add a feature as a vertical slice.

**When to use it:** Adding a new business capability to an established Spring Boot application.

**Complete prompt:**

```text
Implement the requested feature as a vertical slice in the current Spring Boot 3.x / Java 21 repository.

Inspect the existing code first. Identify the relevant controller/API boundary, DTOs, application/service logic, domain behavior, persistence components, validation, exception mapping, and tests. Reuse established conventions where they are sound.

Constraints:
- Make the smallest set of cohesive changes needed for the feature.
- Prefer constructor injection.
- Keep HTTP concerns out of domain/application logic.
- Do not expose JPA entities directly through the API unless the project explicitly standardizes on that approach and the tradeoff is justified.
- Use Jakarta validation and Jakarta Persistence where applicable.
- Define transaction boundaries at the service/use-case level rather than scattering transactions through controllers and repositories.
- Preserve backward compatibility unless the requested behavior explicitly changes the contract.
- Avoid new abstractions that have only one trivial implementation.
- Add or update unit and integration tests for meaningful behavior and boundary conditions.

Before modifying code, state the implementation plan and any assumptions. After implementation, compile and run the relevant tests. Report changed files, behavior added, design choices, and any unresolved risks.
```

**Expected AI output:** A focused feature implementation spanning only the layers required, with tests and rationale.

**Optional follow-up prompts:**

- `Add negative-path and concurrency tests for this feature.`
- `Review the feature for API compatibility and transaction-boundary risks.`

**Notes or cautions:** Prefer a complete thin slice over partially building several layers.

---

## Gradle

### 3. Audit and simplify the Gradle build

**Purpose:** Audit and simplify the Gradle build.

**When to use it:** When a Spring Boot build has accumulated plugins, dependencies, custom tasks, or inconsistent configuration.

**Complete prompt:**

```text
Review the current Gradle build as a senior Java build engineer. The project uses Java 21 and Spring Boot 3.x.

Analyze all Gradle build files, settings, version catalogs, convention plugins, wrapper configuration, and relevant CI build commands. Identify:
- unused or redundant plugins;
- dependencies declared in the wrong configuration;
- version overrides that fight Spring Boot dependency management;
- duplicated repository or compiler configuration;
- non-reproducible dependency declarations;
- tasks that bypass normal lifecycle behavior;
- configuration-cache or build-cache blockers where relevant;
- test task configuration that can be simplified;
- Java toolchain inconsistencies;
- dependency constraints that are unexplained or obsolete.

Do not modify files initially. Produce a prioritized set of recommendations with rationale, risk, and expected benefit. Separate safe cleanup from changes that could alter runtime behavior.

After I approve recommendations, apply them incrementally, run `./gradlew clean build`, and fix only failures caused by the approved changes.
```

**Expected AI output:** A build audit that distinguishes safe cleanup from behavior-changing changes and provides an incremental remediation plan.

**Optional follow-up prompts:**

- `Apply only the safe cleanup items.`
- `Show why each explicitly versioned dependency is or is not necessary.`

**Notes or cautions:** Do not upgrade dependencies merely because newer versions exist.

---

### 4. Diagnose a Gradle dependency conflict

**Purpose:** Diagnose a Gradle dependency conflict.

**When to use it:** When compilation, startup, or tests fail because incompatible library versions are being resolved.

**Complete prompt:**

```text
Diagnose the dependency/version conflict in this Spring Boot 3.x Gradle project.

Use the repository and Gradle's dependency-insight capabilities to determine the actual resolved dependency graph. Do not guess from `build.gradle` declarations alone.

Work methodically:
1. Identify the failing class, method, package, or linkage error.
2. Determine which dependency supplies it at compile time and runtime.
3. Run appropriate Gradle reports such as `dependencies` and `dependencyInsight`.
4. Identify which direct or transitive dependency introduced the incompatible version.
5. Check whether Spring Boot's dependency management already defines a compatible version.
6. Recommend the least invasive fix: remove an unnecessary override, exclude a transitive dependency only when justified, align a platform/BOM, or upgrade a direct dependency if required.
7. Explain why the fix is safe and what could regress.

Make the minimal approved change, then run the narrow failing task and `./gradlew clean build`. Report the resolved versions before and after.
```

**Expected AI output:** A root-cause dependency analysis backed by Gradle resolution data and a minimal verified fix.

**Optional follow-up prompts:**

- `Show the exact dependency path that introduced the conflicting artifact.`
- `Check whether this conflict also affects testRuntimeClasspath.`

**Notes or cautions:** Prefer dependency alignment over broad exclusions.

---

## Configuration

### 5. Review application configuration

**Purpose:** Review application configuration.

**When to use it:** When configuration has grown across YAML/properties, environment variables, and code.

**Complete prompt:**

```text
Review configuration management in the current Spring Boot 3.x application.

Inspect `application.yml`/`application.properties`, profile-specific files, `@ConfigurationProperties` classes, environment-variable usage, secret references, and deployment configuration that is present in the workspace.

Evaluate:
- type-safe configuration versus scattered `@Value`;
- naming and grouping of properties;
- defaults that are safe for production;
- validation of required properties;
- duplication across profiles;
- secrets accidentally committed or logged;
- environment-specific values embedded in code;
- ambiguous precedence between files, environment variables, and command-line arguments;
- configuration that should be owned by infrastructure rather than application code.

Do not edit first. Produce a concrete refactoring plan that preserves behavior. For each proposed property move or rename, identify compatibility implications.

After approval, refactor incrementally using immutable or constructor-bound `@ConfigurationProperties` patterns appropriate to the current Spring Boot version. Add tests for configuration binding/validation where valuable. Compile and run tests.
```

**Expected AI output:** A configuration audit and a low-risk migration toward cohesive, validated, type-safe configuration.

**Optional follow-up prompts:**

- `Convert the highest-risk `@Value` group to `@ConfigurationProperties`.`
- `Identify every configuration value that could expose a secret.`

**Notes or cautions:** Property renames can break deployment manifests even when application tests pass.

---

### 6. Introduce validated configuration properties

**Purpose:** Introduce validated configuration properties.

**When to use it:** When several related external settings need a maintainable type-safe representation.

**Complete prompt:**

```text
Create a type-safe Spring Boot configuration model for the related settings already used by this feature.

Inspect existing property names and consumers before changing anything. Use Spring Boot 3.x conventions and Java 21. Prefer a cohesive `@ConfigurationProperties` type over multiple `@Value` fields.

Requirements:
- Keep the property prefix stable unless there is a strong reason to change it.
- Model required versus optional values explicitly.
- Use Jakarta Bean Validation for invalid or missing startup configuration where failure-fast behavior is appropriate.
- Use domain-appropriate Java types such as `Duration`, `DataSize`, URI, enums, or records where they improve correctness.
- Avoid embedding secrets in defaults.
- Update consumers to use constructor injection.
- Add focused binding/validation tests.
- Preserve deployment compatibility or clearly document any required configuration migration.

Implement incrementally, compile, and run the relevant tests. Explain why each property belongs in this configuration group.
```

**Expected AI output:** A cohesive configuration-properties model with validated binding, updated consumers, and tests.

**Optional follow-up prompts:**

- `Add metadata/documentation for these configuration properties.`
- `Review which defaults should be removed for production safety.`

**Notes or cautions:** Do not turn every single property into a separate configuration class.

---

## Profiles

### 7. Reduce Spring profile complexity

**Purpose:** Reduce Spring profile complexity.

**When to use it:** When many profiles contain overlapping beans or configuration and behavior is hard to reason about.

**Complete prompt:**

```text
Audit Spring profile usage in this repository and reduce unnecessary profile-driven branching.

Inspect `@Profile`, profile-specific configuration files, test profiles, deployment manifests, and conditional bean definitions. Build a map of what each profile changes.

Evaluate whether each use is truly an environment distinction or whether it would be better represented by:
- external configuration;
- feature flags;
- `@ConditionalOnProperty`;
- explicit test configuration;
- infrastructure-specific adapters selected by configuration.

Do not modify code first. Identify profile combinations that are ambiguous, mutually exclusive, or difficult to test. Recommend a simpler model that keeps production behavior explicit.

After approval, make incremental changes and add tests that prove bean selection/configuration for the important cases. Run the relevant Spring context tests and the full build.
```

**Expected AI output:** A profile map, simplification recommendations, and verified reduction of environment-coupled behavior.

**Optional follow-up prompts:**

- `Replace only the most problematic profile with property-based conditional configuration.`
- `List all tests whose behavior depends on an active profile.`

**Notes or cautions:** Profiles are useful, but using them as a general-purpose feature-toggle system creates hidden combinations.

---

### 8. Create a safe local-development profile

**Purpose:** Create a safe local-development profile.

**When to use it:** When developers need local defaults without weakening production configuration.

**Complete prompt:**

```text
Create or improve the local-development configuration for this Spring Boot application without making production defaults less safe.

Inspect the existing configuration and deployment assumptions. Add only local conveniences that are clearly isolated from production.

Requirements:
- Never commit real credentials.
- Keep production-safe defaults in the base configuration.
- Put local-only endpoints, ports, logging verbosity, or emulator settings in an explicit local configuration.
- Prefer environment variables for developer-specific secrets.
- Do not silently substitute insecure authentication/authorization behavior unless the project explicitly requires a local stub and it is strongly isolated.
- Ensure tests do not accidentally depend on the local profile.
- Document the exact activation mechanism in the repository's existing developer documentation if appropriate.

Validate startup/configuration binding and run tests. Explain which settings are local-only and why.
```

**Expected AI output:** A clearly isolated local profile that improves developer experience without contaminating production behavior.

**Optional follow-up prompts:**

- `Check whether Docker Compose or Testcontainers can eliminate some local-only configuration.`
- `Review the local profile for accidental security bypasses.`

**Notes or cautions:** Never make `local` the implicit production fallback.

---

## Dependency Management

### 9. Review Spring Boot dependencies

**Purpose:** Review Spring Boot dependencies.

**When to use it:** When dependencies need a security, maintainability, or scope audit.

**Complete prompt:**

```text
Audit the dependencies of this Spring Boot 3.x / Java 21 Gradle project.

Inspect direct and transitive dependencies and classify them by purpose. For each direct dependency, determine:
- whether it is actually used;
- whether Spring Boot already manages its version;
- whether its Gradle configuration is appropriate (`implementation`, `runtimeOnly`, `testImplementation`, etc.);
- whether a smaller starter or library would reduce unnecessary surface area;
- whether it duplicates another library's responsibility;
- whether it creates Jakarta/Javax compatibility risk;
- whether it has known project-specific upgrade constraints visible in the repository.

Do not upgrade or remove anything initially. Produce a prioritized table of recommendations with evidence from source usage and the resolved dependency graph.

After approval, apply changes in small batches and run `./gradlew clean build` after each meaningful batch.
```

**Expected AI output:** A dependency inventory with evidence-based keep/remove/align recommendations.

**Optional follow-up prompts:**

- `Remove only dependencies proven unused and verify the build.`
- `Find explicit versions that should defer to Spring Boot dependency management.`

**Notes or cautions:** Static usage searches can miss reflective/framework usage; verify before removal.

---

### 10. Migrate from javax to Jakarta dependencies

**Purpose:** Migrate from javax to Jakarta dependencies.

**When to use it:** When upgrading or repairing a Spring Boot 3.x codebase that still contains legacy Java EE dependencies.

**Complete prompt:**

```text
Find and remediate legacy `javax.*` API dependencies and imports that are incompatible or unnecessary in this Spring Boot 3.x application.

First inventory all `javax.*` imports, Gradle dependencies, generated sources, configuration, persistence mappings, validation annotations, servlet APIs, and third-party integrations.

Classify each occurrence:
- must migrate to `jakarta.*`;
- legitimately remains `javax.*` because it is a Java SE API such as `javax.crypto`;
- generated/vendor code that should not be edited directly;
- third-party library incompatibility requiring a dependency upgrade or adapter.

Do not perform blind search-and-replace. Propose the migration in dependency-safe increments. Then make approved changes, compile after each logical group, and run tests.

Report any library that prevents a complete Spring Boot 3.x/Jakarta migration and recommend the least disruptive resolution.
```

**Expected AI output:** A precise Jakarta migration that avoids incorrectly replacing valid Java SE `javax` packages.

**Optional follow-up prompts:**

- `Migrate only persistence and validation APIs first.`
- `Identify third-party libraries still tied to Java EE namespaces.`

**Notes or cautions:** Not every `javax` package was renamed to `jakarta`.

---

## Database Reverse Engineering

### 11. Reverse engineer an existing schema

**Purpose:** Reverse engineer an existing schema.

**When to use it:** When building a Spring Boot persistence layer against a pre-existing relational schema.

**Complete prompt:**

```text
Reverse engineer the database model represented by the schema/migrations available in this workspace into a maintainable Spring Boot 3.x persistence design.

Do not mechanically create one JPA entity per table before understanding the schema. First analyze:
- primary and foreign keys;
- unique constraints;
- nullability;
- indexes;
- lookup/reference tables;
- join tables;
- inheritance-like patterns;
- audit columns;
- optimistic-lock candidates;
- database-generated values;
- views, triggers, and stored procedures that affect application behavior.

Produce a proposed entity/aggregate mapping and explicitly call out tables that should not become normal mutable entities.

Use Jakarta Persistence. Avoid unnecessary bidirectional relationships. Prefer explicit fetch behavior and avoid `EAGER` collections. Preserve database constraints rather than relying only on application validation.

After approval, generate mappings incrementally, add focused persistence tests against a realistic database via the project's established integration-test approach, and run the build.
```

**Expected AI output:** A schema-informed persistence model rather than a naïve table-to-class translation.

**Optional follow-up prompts:**

- `Generate mappings for one aggregate first and validate SQL behavior.`
- `Identify schema constraints that should also be represented in application validation.`

**Notes or cautions:** Database semantics such as triggers and generated columns must be understood before ORM writes are enabled.

---

### 12. Assess a legacy schema before JPA mapping

**Purpose:** Assess a legacy schema before JPA mapping.

**When to use it:** When deciding whether a legacy database is suitable for direct ORM mapping.

**Complete prompt:**

```text
Assess whether the legacy relational schema in this workspace is safe and practical to map with Jakarta Persistence.

Do not generate entities yet. Analyze the schema for ORM hazards:
- tables without stable primary keys;
- composite keys;
- nullable or inconsistent foreign keys;
- trigger-managed columns;
- shared tables written by other applications;
- denormalized structures;
- polymorphic relationships;
- stored-procedure-centric workflows;
- optimistic locking feasibility;
- very large collections;
- database-specific data types;
- read-only views.

For each hazard, recommend one of: normal JPA mapping, read-only mapping, native SQL/JdbcClient, stored-procedure integration, adapter layer, schema change, or deliberate non-mapping.

Explain tradeoffs in maintainability, correctness, transaction behavior, and performance. Return a prioritized mapping strategy before any code changes.
```

**Expected AI output:** A go/no-go mapping assessment with per-table integration strategies.

**Optional follow-up prompts:**

- `Design the persistence approach for the highest-risk table.`
- `Identify where JPA would hide important database behavior.`

**Notes or cautions:** Using JPA everywhere is not a goal; choose the persistence mechanism that makes behavior clearest.

---

## Entity Generation

### 13. Generate a JPA entity from an approved table

**Purpose:** Generate a JPA entity from an approved table.

**When to use it:** When a table has already been analyzed and needs a production-quality entity mapping.

**Complete prompt:**

```text
Generate a Jakarta Persistence entity for the approved table using the schema and repository conventions already present.

Requirements:
- Java 21 and Spring Boot 3.x.
- Use `jakarta.persistence` imports.
- Map identifiers and generation strategy to actual database behavior.
- Preserve nullability, length, precision/scale, uniqueness, and column names where mapping is not implicit.
- Use appropriate Java temporal and numeric types.
- Add `@Version` only if the schema and update model support optimistic locking.
- Default relationships to the least surprising fetch behavior; never make collections eager.
- Avoid bidirectional relationships unless navigation is genuinely required in both directions.
- Do not put API serialization annotations on the entity unless the architecture explicitly exposes entities.
- Implement equality/hash semantics safely for the chosen identifier lifecycle; explain the decision.
- Do not add setters indiscriminately if domain invariants require controlled mutation.

Add a focused persistence test that proves the important mapping. Compile and run tests. Report generated SQL concerns such as unexpected joins or extra selects.
```

**Expected AI output:** A schema-faithful entity plus a persistence test and explanation of identity/relationship choices.

**Optional follow-up prompts:**

- `Add the related entity mapping without creating a bidirectional association unless necessary.`
- `Review this entity for unsafe equality/hashCode behavior.`

**Notes or cautions:** Entity generation should follow an approved schema analysis, not infer missing constraints.

---

### 14. Review JPA entity relationships

**Purpose:** Review JPA entity relationships.

**When to use it:** When an entity graph has become complex or is causing serialization/performance problems.

**Complete prompt:**

```text
Review all JPA relationships in the selected entity area as a persistence specialist.

For every `@OneToOne`, `@OneToMany`, `@ManyToOne`, and `@ManyToMany`, evaluate:
- whether the relationship is required in the object model;
- owning side correctness;
- cascade semantics;
- orphan removal;
- fetch behavior;
- collection type;
- bidirectional synchronization;
- lifecycle coupling;
- potential N+1 queries;
- serialization hazards;
- equality/hashCode interaction;
- aggregate-boundary implications.

Do not modify mappings initially. Recommend the smallest set of changes that improves correctness and query behavior. Include likely SQL consequences.

After approval, apply changes incrementally and add persistence tests that exercise load, insert/update, and deletion semantics relevant to the relationship. Run the tests and inspect query behavior where tooling permits.
```

**Expected AI output:** A relationship-by-relationship review with ORM and aggregate-lifecycle rationale.

**Optional follow-up prompts:**

- `Fix only unsafe cascade/orphan-removal settings.`
- `Design a query that loads the required graph without changing default fetch types.`

**Notes or cautions:** Changing fetch type to EAGER is rarely a valid N+1 fix.

---

## Repository Generation

### 15. Generate a focused Spring Data repository

**Purpose:** Generate a focused Spring Data repository.

**When to use it:** When an entity needs repository access without overbuilding a data-access API.

**Complete prompt:**

```text
Create or refine the Spring Data repository needed by this use case.

Inspect the entity model and actual service/query requirements first. Do not generate CRUD methods that Spring Data already provides or speculative finder methods.

Requirements:
- Extend the narrowest appropriate Spring Data repository interface used by the project.
- Add only queries required by current behavior.
- Prefer derived queries when they remain clear; use explicit JPQL/native SQL when it communicates intent better.
- Use projections for read models when loading full entities is unnecessary.
- Make pagination explicit for potentially unbounded result sets.
- Avoid returning `null`; use repository conventions such as `Optional` where semantically appropriate.
- Do not hide complex business logic inside repository default methods.
- Consider locking only when the concurrency requirement justifies it.
- Explain index expectations for nontrivial queries.

Add focused repository tests for custom queries and edge cases. Run the relevant tests.
```

**Expected AI output:** A minimal repository surface aligned to actual use cases, with tested query behavior.

**Optional follow-up prompts:**

- `Convert the heaviest read query to a projection.`
- `Review custom repository methods for missing pagination or index assumptions.`

**Notes or cautions:** Repository interfaces should describe persistence needs, not become a second service layer.

---

### 16. Review repository query efficiency

**Purpose:** Review repository query efficiency.

**When to use it:** When Spring Data methods work functionally but database efficiency is uncertain.

**Complete prompt:**

```text
Review the selected Spring Data repositories for query efficiency and correctness.

Trace each nontrivial repository method to its callers and determine expected cardinality and access pattern. Evaluate:
- N+1 query risk;
- accidental full-table scans;
- unbounded list results;
- unnecessary entity hydration;
- join-fetch pagination problems;
- incorrect count-query behavior;
- repeated existence/load round trips;
- database functions that defeat indexes;
- missing projections;
- pessimistic locks that are broader than necessary.

Use available SQL logging, tests, query plans, or schema indexes where possible. Do not optimize based only on method names.

Return prioritized findings first. For approved changes, make the smallest query/repository modifications, add regression/performance-oriented integration tests where practical, and run the build.
```

**Expected AI output:** Evidence-based repository optimization recommendations and verified targeted changes.

**Optional follow-up prompts:**

- `Inspect the execution plan for the highest-volume query.`
- `Replace full-entity loading with the smallest useful projection.`

**Notes or cautions:** Do not add indexes blindly from application code; coordinate schema changes with migrations.

---

## DTO Generation

### 17. Generate API request and response DTOs

**Purpose:** Generate API request and response DTOs.

**When to use it:** When an API contract should be separated from persistence/domain objects.

**Complete prompt:**

```text
Create request and response DTOs for the selected Spring Boot API operation.

Inspect the existing API conventions and domain model. Design DTOs around the external contract, not as automatic copies of JPA entities.

Requirements:
- Use Java 21 records when they fit immutable data-carrier semantics and project conventions.
- Separate request and response models when their responsibilities differ.
- Apply Jakarta Bean Validation to request constraints that belong at the API boundary.
- Do not leak persistence-only fields, lazy relationships, internal audit data, or domain implementation details.
- Use stable external names and types.
- Represent optionality deliberately; do not use `Optional` as a DTO field unless the project has a specific convention requiring it.
- Keep business validation in the domain/service layer rather than duplicating it as annotation-only validation.
- Preserve backward compatibility for existing endpoints.

Implement mapping using the project's established approach. Add controller/serialization tests for contract-critical fields and validation.
```

**Expected AI output:** Contract-oriented DTOs with validation and mapping, independent of entity structure.

**Optional follow-up prompts:**

- `Split create and update requests where their validation semantics differ.`
- `Review the response DTO for accidental persistence leakage.`

**Notes or cautions:** DTOs are API contracts; changing them can be more consequential than changing internal entities.

---

### 18. Design DTOs for partial updates

**Purpose:** Design DTOs for partial updates.

**When to use it:** When implementing PATCH-like behavior without ambiguous null semantics.

**Complete prompt:**

```text
Design the request model and update flow for a partial-update endpoint in this Spring Boot application.

First determine the existing API semantics and whether JSON Merge Patch, JSON Patch, or a project-specific PATCH contract is appropriate. Do not treat `null` and “field absent” as equivalent unless that is explicitly the contract.

Address:
- how omitted fields differ from fields explicitly set to null;
- validation of changed fields;
- immutable versus mutable domain objects;
- authorization of field-level changes if applicable;
- optimistic locking/version conflicts;
- mapping without overwriting unspecified values;
- backward-compatible response behavior.

Recommend the simplest contract that preserves unambiguous semantics. After approval, implement it incrementally with request DTOs, service logic, exception mapping, and tests for absent/null/value cases.
```

**Expected AI output:** An unambiguous partial-update contract and implementation with edge-case tests.

**Optional follow-up prompts:**

- `Add optimistic-lock conflict handling to the update flow.`
- `Test explicit-null versus omitted-field behavior.`

**Notes or cautions:** Ordinary DTOs with nullable fields often cannot distinguish omission from explicit null.

---

## MapStruct

### 19. Introduce MapStruct for nontrivial mappings

**Purpose:** Introduce MapStruct for nontrivial mappings.

**When to use it:** When manual DTO/entity mapping is repetitive enough to justify generated mapping code.

**Complete prompt:**

```text
Evaluate whether MapStruct is justified for the selected mappings, and introduce it only if it reduces meaningful repetitive mapping code.

Inspect current manual mappings and Gradle configuration first. If MapStruct is justified:
- use a version compatible with the current Spring Boot/Java toolchain;
- configure Gradle annotation processing correctly;
- use Spring component integration only where injection is actually useful;
- make unmapped target properties visible rather than silently ignored;
- keep business decisions and repository lookups out of generated mappers;
- use explicit mapping methods for differing field names or nested structures;
- avoid cycles caused by bidirectional JPA graphs;
- keep entity mutation semantics safe.

Migrate one cohesive mapping area first. Add mapping tests for transformations with business significance. Compile and run tests. Explain why MapStruct is preferable here to straightforward manual mapping.
```

**Expected AI output:** A justified, correctly configured MapStruct integration with one proven mapping slice.

**Optional follow-up prompts:**

- `Convert the next repetitive mapper using the same conventions.`
- `Enable stricter unmapped-property reporting and fix legitimate gaps.`

**Notes or cautions:** MapStruct should remove boilerplate, not become a hidden business-logic layer.

---

### 20. Review existing MapStruct mappers

**Purpose:** Review existing MapStruct mappers.

**When to use it:** When generated mappings are hard to understand or silently drop fields.

**Complete prompt:**

```text
Audit the MapStruct mappers in this repository for correctness and maintainability.

Check:
- unmapped source/target properties;
- accidental field loss during updates;
- null-value strategies;
- collection and nested-object behavior;
- use of `@MappingTarget`;
- entity relationship mapping that could trigger lazy loads;
- expressions or default methods containing business logic;
- mapper-to-mapper dependencies;
- component model consistency;
- generated code assumptions that differ from domain invariants.

Do not edit initially. Identify high-risk mappings and show concrete examples of incorrect or fragile behavior.

For approved fixes, change mapper configuration/methods incrementally, add focused tests, inspect generated behavior when useful, compile, and run tests.
```

**Expected AI output:** A risk-focused mapper audit with tests protecting important transformations.

**Optional follow-up prompts:**

- `Fix update mappings that overwrite fields with null.`
- `Identify mapper methods that may initialize lazy JPA relationships.`

**Notes or cautions:** Generated code is still application code; validate semantics rather than trusting compilation.

---

## Service Layer

### 21. Design a service/use-case boundary

**Purpose:** Design a service/use-case boundary.

**When to use it:** When business logic is spread between controllers, repositories, and utility classes.

**Complete prompt:**

```text
Refactor the selected feature toward a clear application/service boundary.

Trace the full request/use-case flow before editing. Identify which logic is:
- transport-specific;
- orchestration/application logic;
- domain/business logic;
- persistence/infrastructure logic.

Propose a service design that makes the use case explicit without creating unnecessary interfaces or pass-through layers. Prefer constructor injection. Keep controllers thin, repositories persistence-focused, and transaction boundaries at the use-case/service level.

Do not move code mechanically. Preserve domain invariants and behavior. If an interface has only one implementation, keep it only when it provides a meaningful architectural boundary, test seam, or alternate implementation requirement.

After approval, refactor in small steps, keeping tests green. Add tests where current behavior is insufficiently protected. Run the relevant tests after each logical change.
```

**Expected AI output:** A clearer service boundary with reduced leakage between HTTP, business, and persistence concerns.

**Optional follow-up prompts:**

- `Move only the first use case into the proposed service structure.`
- `Review service interfaces and remove ones that add no architectural value.`

**Notes or cautions:** Layering is a means to control responsibilities, not a requirement to create one class per layer.

---

### 22. Review service class responsibilities

**Purpose:** Review service class responsibilities.

**When to use it:** When a service has become large, highly coupled, or difficult to test.

**Complete prompt:**

```text
Review the selected Spring service as a senior Java architect.

Measure responsibility rather than class size alone. Analyze:
- number of distinct use cases;
- injected dependencies and what they imply;
- transaction scope;
- domain rules versus orchestration;
- repeated mapping/validation;
- external I/O mixed with database work;
- conditional complexity;
- duplicated workflows;
- test setup complexity;
- hidden temporal coupling between methods.

Do not modify code first. Propose responsibility boundaries and an incremental extraction order. Avoid “one method = one class” fragmentation.

For each recommendation, explain the architectural reason, expected testability/maintainability benefit, and risk. After approval, refactor one responsibility at a time and run tests after each step.
```

**Expected AI output:** A responsibility-based decomposition plan rather than arbitrary class splitting.

**Optional follow-up prompts:**

- `Extract the highest-coupling responsibility first.`
- `Show which injected dependencies should move with each extracted responsibility.`

**Notes or cautions:** Large services are not automatically bad; mixed reasons to change are the stronger signal.

---

## REST Controllers

### 23. Create a production REST endpoint

**Purpose:** Create a production REST endpoint.

**When to use it:** When implementing a new HTTP operation in an existing Spring Boot API.

**Complete prompt:**

```text
Implement the requested REST endpoint in the current Spring Boot 3.x application.

First inspect existing API conventions, routing, error format, security, DTOs, and tests. Preserve established contract conventions where they are sound.

Requirements:
- Keep the controller focused on HTTP concerns.
- Use request/response DTOs rather than exposing JPA entities.
- Apply Jakarta Bean Validation at the request boundary.
- Delegate business behavior to an application/service component.
- Return semantically correct HTTP status codes and headers.
- Handle not-found, conflict, validation, and domain failures through centralized exception mapping.
- Preserve idempotency semantics appropriate to the HTTP method.
- Avoid leaking stack traces or internal exception messages.
- Consider pagination for collection endpoints.
- Do not add transactions to the controller.

Add focused MVC/API tests for success and important failure cases. Compile and run tests. Report any contract decisions that could affect clients.
```

**Expected AI output:** A thin, contract-focused controller endpoint with DTOs, validation, error handling, and tests.

**Optional follow-up prompts:**

- `Add conditional request/ETag support if the resource needs concurrency-aware updates.`
- `Review the endpoint against HTTP idempotency and status-code semantics.`

**Notes or cautions:** Do not use HTTP 200 as a universal success/failure envelope.

---

### 24. Review REST API consistency

**Purpose:** Review REST API consistency.

**When to use it:** When an API has accumulated inconsistent routes, statuses, payloads, or error behavior.

**Complete prompt:**

```text
Review the REST controllers in the selected API area for contract consistency.

Evaluate:
- resource naming and URI structure;
- HTTP method semantics;
- status codes;
- request/response DTO consistency;
- pagination/filter/sort conventions;
- validation behavior;
- error representation;
- idempotency;
- content types;
- versioning/backward compatibility;
- accidental exposure of persistence models;
- controller logic that belongs in services.

Do not change public contracts initially. Categorize findings as:
1. internal refactors with no contract change;
2. backward-compatible contract improvements;
3. breaking changes requiring versioning/migration.

Provide specific examples from the repository and a staged remediation plan. Implement only approved categories and add contract regression tests.
```

**Expected AI output:** A compatibility-aware REST consistency review and staged remediation plan.

**Optional follow-up prompts:**

- `Apply only non-breaking internal controller refactors.`
- `Design a migration path for the highest-value breaking contract change.`

**Notes or cautions:** API cleanup can impose significant client costs; classify compatibility before editing.

---

## Validation

### 25. Separate boundary and business validation

**Purpose:** Separate boundary and business validation.

**When to use it:** When validation annotations and service checks are mixed or duplicated.

**Complete prompt:**

```text
Review validation for the selected use case and separate API-boundary validation from business/domain invariants.

Inventory Jakarta Bean Validation annotations, custom validators, service checks, database constraints, and domain methods.

Classify each rule:
- syntactic/structural request validation;
- business invariant;
- cross-aggregate or database-dependent rule;
- persistence constraint;
- security/authorization rule.

Recommend where each rule should live and why. Avoid custom Bean Validation constraints that require repositories unless that is clearly the best project-specific design.

After approval, refactor incrementally so invalid requests fail consistently while business rules remain enforceable even outside HTTP entry points. Add tests at the correct level and run the build.
```

**Expected AI output:** A validation model where each rule is enforced at the appropriate boundary.

**Optional follow-up prompts:**

- `Move one business invariant out of DTO annotations into domain/service logic.`
- `Align database constraints with application validation for this field.`

**Notes or cautions:** Bean Validation should not become a substitute for domain invariants or authorization.

---

### 26. Implement cross-field validation

**Purpose:** Implement cross-field validation.

**When to use it:** When validity depends on relationships between multiple request fields.

**Complete prompt:**

```text
Implement the cross-field validation required by the selected request model.

First determine whether the rule is purely structural and belongs at the request boundary or is a business rule that belongs deeper in the application. If it is boundary validation, use a maintainable Jakarta Bean Validation approach.

Requirements:
- produce a clear client-facing validation message;
- attach the violation to the most useful field or object location;
- keep the validator deterministic and free of database/network I/O;
- handle nulls consistently with field-level constraints;
- avoid reflection-heavy generic validators when a typed validator is clearer;
- add tests for valid, invalid, null, and boundary cases.

If the rule is actually a domain invariant, explain why and implement it there instead. Compile and run tests.
```

**Expected AI output:** A correctly placed and thoroughly tested cross-field rule.

**Optional follow-up prompts:**

- `Add tests showing interaction with field-level constraints.`
- `Review whether this rule must also be protected by a database constraint.`

**Notes or cautions:** Do not perform repository lookups from a validation annotation without a compelling reason.

---

## Transactions

### 27. Review transaction boundaries

**Purpose:** Review transaction boundaries.

**When to use it:** When transaction behavior is unclear or `@Transactional` is scattered across the codebase.

**Complete prompt:**

```text
Review transaction boundaries for the selected Spring Boot feature.

Trace each use case from entry point through service, repository, and external integrations. Identify every `@Transactional` annotation and implicit repository transaction.

Evaluate:
- whether the transaction encloses a complete consistency boundary;
- read-only versus write transactions;
- propagation settings;
- isolation requirements;
- self-invocation/proxy limitations;
- lazy loading outside transactions;
- external network calls performed while database locks are held;
- event publication timing;
- retry behavior;
- exception types that affect rollback.

Do not change annotations first. Produce a transaction map and prioritized risks. Recommend the simplest boundary for each use case.

After approval, modify one flow at a time and add integration tests for commit/rollback behavior and concurrency where relevant. Run tests.
```

**Expected AI output:** A use-case-level transaction map with evidence-based boundary corrections.

**Optional follow-up prompts:**

- `Fix the transaction that holds locks during external I/O.`
- `Add a rollback integration test for the highest-risk write flow.`

**Notes or cautions:** More `@Transactional` annotations do not create safer transactions.

---

### 28. Design safe external I/O around a database transaction

**Purpose:** Design safe external I/O around a database transaction.

**When to use it:** When a use case updates the database and also calls another service or broker.

**Complete prompt:**

```text
Redesign the selected use case so database state changes and external I/O have explicit, failure-aware semantics.

Inspect the current transaction and integration flow. Determine what happens if:
- the database commits but the external call fails;
- the external call succeeds but the database rolls back;
- the operation is retried;
- the same message/request is processed twice.

Do not attempt a distributed ACID transaction by default. Evaluate patterns appropriate to the actual requirement, including transactional outbox, after-commit publication, idempotency keys, retries, or compensating actions.

Recommend one design with rationale and operational implications. After approval, implement the smallest viable change, including schema/migration changes if needed, tests for failure windows, and observable logging/metrics. Run the build and relevant integration tests.
```

**Expected AI output:** A failure-aware consistency design for database plus external side effects.

**Optional follow-up prompts:**

- `Implement the transactional outbox for this flow.`
- `Add duplicate-delivery tests and idempotent consumer behavior.`

**Notes or cautions:** Network calls inside long database transactions can amplify lock contention and failure coupling.

---

## Exception Handling

### 29. Create centralized API exception handling

**Purpose:** Create centralized API exception handling.

**When to use it:** When controllers return inconsistent errors or catch exceptions locally.

**Complete prompt:**

```text
Create or refactor centralized exception handling for the selected Spring Boot REST API.

Inspect current exception types, controller catches, validation failures, security behavior, and existing error payloads. Preserve public compatibility where required.

Requirements:
- use a centralized Spring MVC exception-handling mechanism;
- map domain/application exceptions to deliberate HTTP statuses;
- provide a stable error response shape, preferably aligned with existing standards such as Problem Details if the project uses them;
- include a safe correlation/request identifier when available;
- never expose stack traces, SQL details, secrets, or raw internal messages to clients;
- preserve useful validation field errors;
- log unexpected failures once at the appropriate boundary;
- do not catch `Exception` in every controller.

Add MVC tests for representative validation, not-found, conflict, and unexpected failures. Compile and run tests.
```

**Expected AI output:** A centralized, stable, safe error contract with representative tests.

**Optional follow-up prompts:**

- `Migrate one legacy controller away from local try/catch handling.`
- `Review which exception messages are safe for clients versus logs only.`

**Notes or cautions:** Exception mapping is part of the external API contract.

---

### 30. Design domain exception taxonomy

**Purpose:** Design domain exception taxonomy.

**When to use it:** When many generic runtime exceptions obscure expected business failures.

**Complete prompt:**

```text
Review exceptions in the selected domain/application area and design the smallest useful exception taxonomy.

Trace where exceptions are created, translated, logged, and exposed. Distinguish:
- expected business rejections;
- not-found conditions;
- concurrency/conflict conditions;
- integration failures;
- programming defects/unexpected failures.

Do not create a class hierarchy for every error message. Recommend exception types only where callers or boundaries need distinct behavior.

Ensure exceptions preserve useful causes, avoid sensitive data, and map cleanly at API/integration boundaries. After approval, refactor incrementally and update tests to assert behavior rather than brittle full message text.
```

**Expected AI output:** A restrained exception model that improves boundary behavior without class proliferation.

**Optional follow-up prompts:**

- `Replace generic `RuntimeException` only in the highest-value flows.`
- `Map the approved exception types to the existing API error contract.`

**Notes or cautions:** Exception classes should represent behaviorally distinct failure categories.

---

## Logging

### 31. Review production logging

**Purpose:** Review production logging.

**When to use it:** When logs are noisy, inconsistent, or missing diagnostic context.

**Complete prompt:**

```text
Audit logging in the selected Spring Boot application area for production usefulness.

Inspect log statements, exception logging, MDC/correlation context, HTTP logging, persistence logging, and configuration.

Identify:
- duplicate logging of the same exception;
- missing identifiers needed to trace a request or business operation;
- sensitive data or credentials in logs;
- excessive INFO logging in hot paths;
- expensive string construction;
- misleading severity levels;
- raw request/response bodies that should not be logged;
- stack traces for expected business outcomes;
- missing structured fields where the logging stack supports them.

Do not add logging everywhere. Recommend a minimal event-oriented logging strategy. After approval, make incremental changes and add tests only where logging behavior is contractually/operationally important. Run tests.
```

**Expected AI output:** A production-oriented logging plan emphasizing signal, traceability, and data safety.

**Optional follow-up prompts:**

- `Remove duplicate exception logging from one request path.`
- `Add correlation context propagation for incoming HTTP requests.`

**Notes or cautions:** Logs are operational data; treat personal data, tokens, and secrets as sensitive.

---

### 32. Add traceable business-operation logging

**Purpose:** Add traceable business-operation logging.

**When to use it:** When an important asynchronous or multi-step operation is difficult to diagnose.

**Complete prompt:**

```text
Add operational logging for the selected business operation so engineers can reconstruct its lifecycle without logging sensitive payloads.

Trace the operation end-to-end and identify a stable correlation/business identifier. Log only meaningful state transitions and failure points.

Requirements:
- use parameterized logging;
- include stable identifiers as structured/MDC context when supported;
- do not log access tokens, credentials, full personal records, or arbitrary request bodies;
- avoid logging the same exception at multiple layers;
- distinguish expected retryable failures from terminal failures;
- keep high-volume per-item detail below INFO unless operationally justified.

Implement consistently with the repository's logging framework. Run tests and explain the chosen events and severity levels.
```

**Expected AI output:** Concise lifecycle logging that makes a production operation traceable.

**Optional follow-up prompts:**

- `Add metrics for the same success/failure transitions.`
- `Review this flow for log volume under peak throughput.`

**Notes or cautions:** Correlation IDs are useful only if propagated consistently across boundaries.

---

## Testing

### 33. Build a balanced test suite for a feature

**Purpose:** Build a balanced test suite for a feature.

**When to use it:** When a feature needs meaningful coverage without relying entirely on full-context tests.

**Complete prompt:**

```text
Review the selected Spring Boot feature and create a balanced test strategy based on risk and framework boundaries.

Use Java 21, JUnit Jupiter, and the repository's established testing libraries. Include explicit imports in all Java code.

Classify tests into:
- pure unit tests for business logic;
- focused Spring slice tests where Spring configuration/serialization/data behavior matters;
- integration tests for database or external-adapter behavior;
- minimal full-context tests only where multiple framework layers must be proven together.

Avoid mocking value objects and simple data structures. Mock external collaborators at unit-test boundaries, not the class under test. Prefer realistic database integration testing when SQL/JPA behavior matters.

Implement the highest-value missing tests first. Run the narrow tests and then the relevant Gradle verification task. Report what risks remain untested.
```

**Expected AI output:** A risk-based test suite with fast unit tests and targeted framework/integration coverage.

**Optional follow-up prompts:**

- `Replace an over-mocked service test with a clearer unit test.`
- `Add a persistence integration test for the query most likely to regress.`

**Notes or cautions:** Test count is not the objective; protect behavior and framework assumptions.

---

### 34. Replace brittle @SpringBootTest usage

**Purpose:** Replace brittle @SpringBootTest usage.

**When to use it:** When many tests load the entire application context unnecessarily.

**Complete prompt:**

```text
Audit uses of `@SpringBootTest` in this repository and reduce unnecessary full-context testing.

For each test, determine what behavior it actually needs:
- plain JUnit unit test;
- `@WebMvcTest` or equivalent MVC slice;
- `@DataJpaTest`;
- focused configuration test;
- integration test requiring real infrastructure/Testcontainers;
- genuine full application context.

Do not replace tests mechanically. Preserve coverage of bean wiring and cross-layer behavior where it is intentional.

Propose changes with expected speed/isolation benefit. After approval, convert tests incrementally, run each converted test, then run the complete suite. Include explicit Java imports in any generated code.
```

**Expected AI output:** A leaner test suite that uses Spring context only where framework behavior is under test.

**Optional follow-up prompts:**

- `Convert the slowest eligible full-context test first.`
- `Identify the small number of tests that should remain `@SpringBootTest` and explain why.`

**Notes or cautions:** Overusing slices can also miss important wiring; retain deliberate end-to-end context tests.

---

## Refactoring

### 35. Refactor safely with characterization tests

**Purpose:** Refactor safely with characterization tests.

**When to use it:** When legacy Spring code needs structural improvement but behavior is poorly documented.

**Complete prompt:**

```text
Refactor the selected legacy Spring Boot code without changing externally observable behavior.

Before editing:
1. Trace current callers and dependencies.
2. Identify public/API/database behavior that must remain stable.
3. Add characterization tests around important existing behavior, including awkward edge cases that callers may rely on.
4. Identify one structural problem to improve first.

Refactor in small, compiling steps. Prefer extracting responsibilities and clarifying names over introducing new frameworks or patterns. Keep constructor injection and existing architectural boundaries unless there is a justified reason to change them.

After each logical step, run the narrow tests. At the end, run the relevant Gradle test/build tasks and summarize exactly what changed structurally versus behaviorally.
```

**Expected AI output:** A behavior-preserving incremental refactor protected by characterization tests.

**Optional follow-up prompts:**

- `Refactor the next responsibility while keeping the characterization tests unchanged.`
- `Now identify behavior that should be deliberately changed in a separate change set.`

**Notes or cautions:** Do not combine broad cleanup with behavior changes unless necessary.

---

### 36. Remove unnecessary Spring abstractions

**Purpose:** Remove unnecessary Spring abstractions.

**When to use it:** When an application contains interfaces, factories, wrappers, or configuration that add indirection without value.

**Complete prompt:**

```text
Review the selected Spring Boot area for accidental complexity and unnecessary abstraction.

Look for:
- one-implementation interfaces with no meaningful boundary;
- pass-through services;
- repositories wrapped by repositories;
- factories that only call constructors;
- configuration classes that merely expose components already discoverable;
- generic utility layers that obscure domain language;
- patterns introduced for hypothetical future requirements.

For each candidate, explain whether the abstraction provides a real substitution boundary, infrastructure isolation, test seam, domain concept, or framework integration benefit.

Do not simplify blindly. Produce a prioritized list first. After approval, remove one abstraction at a time, update tests, compile, and verify behavior.
```

**Expected AI output:** A maintainability-focused simplification plan with evidence for what should stay or go.

**Optional follow-up prompts:**

- `Remove only the highest-confidence redundant abstraction.`
- `Review whether any tests rely on mocks that exist solely because of unnecessary interfaces.`

**Notes or cautions:** Simplicity means fewer concepts while preserving useful boundaries, not merely fewer files.

---

## Performance

### 37. Investigate a slow Spring Boot endpoint

**Purpose:** Investigate a slow Spring Boot endpoint.

**When to use it:** When an endpoint has unacceptable latency or throughput.

**Complete prompt:**

```text
Investigate the selected slow endpoint using evidence before proposing optimizations.

Trace the request through controller, service, persistence, serialization, and external calls. Use available metrics, logs, profiles, SQL output, query plans, and tests. Separate latency into components where possible.

Check for:
- N+1 queries and excessive round trips;
- missing indexes or unbounded queries;
- unnecessary entity hydration;
- blocking external calls;
- repeated serialization/mapping;
- lock contention or oversized transactions;
- thread-pool saturation;
- connection-pool pressure;
- large payloads;
- accidental repeated work;
- inappropriate caching assumptions.

Do not optimize code based on intuition alone. Produce a ranked bottleneck hypothesis with evidence and a measurement plan. Apply only approved changes one at a time and re-measure after each. Run tests and performance checks appropriate to the repository.
```

**Expected AI output:** A measured bottleneck analysis with incremental optimizations tied to evidence.

**Optional follow-up prompts:**

- `Instrument the top latency component so we can measure it directly.`
- `Optimize only the highest-confidence database bottleneck and compare before/after.`

**Notes or cautions:** Do not add caching until the consistency model and invalidation strategy are explicit.

---

### 38. Review JPA performance hazards

**Purpose:** Review JPA performance hazards.

**When to use it:** When persistence behavior may be causing latency, memory, or database load problems.

**Complete prompt:**

```text
Perform a focused JPA/Hibernate performance review of the selected use cases.

Inspect entity mappings, repository queries, transaction boundaries, DTO mapping, and generated SQL where available.

Evaluate:
- N+1 selects;
- EAGER relationships;
- oversized persistence contexts;
- unnecessary dirty checking;
- entity loading for read-only projections;
- batch insert/update opportunities;
- join-fetch cardinality explosions;
- pagination with collection fetch joins;
- Open Session in View dependence;
- lazy initialization workarounds;
- inefficient existence/count patterns.

Do not recommend global fetch-mode changes. Tie every recommendation to a concrete use case and expected SQL behavior.

After approval, implement one optimization at a time, add regression tests, inspect SQL/query counts where practical, and run the build.
```

**Expected AI output:** A use-case-specific Hibernate performance audit with SQL-aware fixes.

**Optional follow-up prompts:**

- `Eliminate the highest-impact N+1 without changing global fetch types.`
- `Convert the read-only path to a projection and compare SQL.`

**Notes or cautions:** ORM performance should be reasoned about in terms of generated SQL and cardinality.

---

## Security

### 39. Review Spring Security configuration

**Purpose:** Review Spring Security configuration.

**When to use it:** When assessing authentication, authorization, and endpoint protection in a Spring Boot service.

**Complete prompt:**

```text
Review the Spring Security configuration in this Spring Boot 3.x application as a production security engineer.

Inspect filter chains, authentication mechanism, OAuth2/OIDC resource-server configuration if present, JWT authority mapping, endpoint authorization, method security, CORS, CSRF decisions, session policy, password handling, and test coverage.

Evaluate:
- default-deny versus accidental permit rules;
- matcher ordering;
- role/authority/scope semantics;
- issuer/audience validation;
- token claim mapping;
- public actuator endpoints;
- CORS breadth;
- CSRF configuration relative to browser/session behavior;
- error responses that leak details;
- security applied only in controllers but bypassable elsewhere.

Do not weaken security to make tests pass. Produce prioritized findings first, including exploitability and operational impact. After approval, apply minimal changes and add security tests for allowed and denied cases. Compile and run tests.
```

**Expected AI output:** A prioritized security-configuration review with verified authorization behavior.

**Optional follow-up prompts:**

- `Add tests proving the most sensitive endpoint is denied by default.`
- `Review JWT authority mapping for roles versus OAuth scopes.`

**Notes or cautions:** Authorization rules must be tested negatively as well as positively.

---

### 40. Secure a new REST endpoint

**Purpose:** Secure a new REST endpoint.

**When to use it:** When adding an endpoint that requires authenticated and authorized access.

**Complete prompt:**

```text
Secure the selected REST endpoint using the authentication/authorization model already established in this repository.

First inspect existing Spring Security configuration and token/authority conventions. Do not invent a second authorization model.

Requirements:
- identify the exact permission, role, or OAuth scope required and justify it using least privilege;
- enforce authorization at the appropriate HTTP and/or method boundary;
- keep business ownership checks in service/domain logic when they depend on resource data;
- return standard 401/403 behavior without leaking details;
- do not trust client-supplied identity fields when identity is available from the authenticated principal;
- add tests for unauthenticated, authenticated-but-forbidden, and authorized requests.

Implement incrementally, compile, and run security/API tests. Explain where each authorization decision is enforced and why.
```

**Expected AI output:** A least-privilege endpoint security implementation with negative-path tests.

**Optional follow-up prompts:**

- `Add resource-ownership authorization for this operation.`
- `Verify the endpoint cannot be reached through an alternate route without the same authorization.`

**Notes or cautions:** Authentication proves identity; it does not by itself authorize the operation.

---

## Production Readiness

### 41. Perform a production-readiness review

**Purpose:** Perform a production-readiness review.

**When to use it:** Before releasing a Spring Boot service into a production environment.

**Complete prompt:**

```text
Perform a production-readiness review of this Spring Boot 3.x / Java 21 service.

Inspect the repository and deployment artifacts. Evaluate only what is relevant to this service, including:
- startup and graceful shutdown;
- liveness/readiness health behavior;
- database migrations;
- connection and HTTP client timeouts;
- retry/backoff policies;
- thread and connection pools;
- configuration validation;
- secret handling;
- structured/correlated logging;
- metrics and alertable failure signals;
- security defaults;
- resource limits/requests if deployment manifests exist;
- JVM/container memory settings;
- idempotency and failure recovery;
- dependency vulnerability/update process;
- backup/recovery assumptions where the service owns data.

Do not make changes initially. Produce a severity-ranked checklist: blocker, high, medium, low. For every blocker/high item, cite the relevant code/config and propose the smallest remediation.

After approval, fix items incrementally and run build/tests plus any available deployment validation.
```

**Expected AI output:** A repository-specific release readiness assessment rather than a generic checklist.

**Optional follow-up prompts:**

- `Fix only production blockers and rerun the readiness review.`
- `Turn the high-severity findings into a release-gate checklist.`

**Notes or cautions:** Do not claim readiness for infrastructure that is not represented or verifiable in the workspace.

---

### 42. Configure graceful shutdown and health semantics

**Purpose:** Configure graceful shutdown and health semantics.

**When to use it:** When a service runs behind an orchestrator/load balancer and must drain safely.

**Complete prompt:**

```text
Review and implement graceful shutdown plus meaningful liveness/readiness behavior for this Spring Boot service.

Inspect its workload: HTTP requests, scheduled jobs, message consumers, database access, and external clients. Determine what “alive” and “ready” actually mean.

Requirements:
- readiness should stop new traffic when the service cannot safely serve it;
- liveness should not depend on every downstream service and cause restart storms;
- graceful shutdown should allow in-flight work to finish within the platform termination window;
- long-running consumers/jobs need explicit stop/drain semantics where applicable;
- health details must not expose sensitive internals publicly;
- configuration must align with deployment probe timing if manifests are present.

Implement the smallest coherent changes, add tests where feasible, and validate configuration/build. Explain probe semantics and shutdown timing assumptions.
```

**Expected AI output:** Operationally meaningful health and shutdown behavior aligned with the workload.

**Optional follow-up prompts:**

- `Review Kubernetes probe settings against the application shutdown/startup timings.`
- `Add a readiness contributor only for the dependency that truly makes the service unable to serve.`

**Notes or cautions:** Do not make liveness depend on transient downstream availability.

---

## Architecture Reviews

### 43. Review a Spring Boot service architecture

**Purpose:** Review a Spring Boot service architecture.

**When to use it:** When assessing layering, coupling, boundaries, and maintainability before significant changes.

**Complete prompt:**

```text
Review this Spring Boot service as a senior Java architect. Do not modify code yet.

Evaluate:
- package/module boundaries;
- controller, service, domain, and persistence responsibilities;
- dependency direction;
- transaction boundaries;
- repository usage;
- DTO/entity separation;
- validation placement;
- exception handling;
- external integration isolation;
- security boundaries;
- test architecture;
- configuration ownership;
- performance-sensitive design choices;
- unnecessary abstractions and framework coupling.

Base findings on actual repository evidence. Distinguish:
1. correctness risks;
2. maintainability risks;
3. scalability/operational risks;
4. stylistic preferences that do not justify churn.

For each material finding, provide evidence, impact, recommended change, rationale, and an incremental migration path. Prioritize the top five changes by value versus risk. Do not recommend a rewrite unless incremental remediation is demonstrably impractical.
```

**Expected AI output:** A repository-grounded architecture assessment with prioritized, incremental recommendations.

**Optional follow-up prompts:**

- `Create an implementation plan for only the top two architecture findings.`
- `Challenge the review: identify recommendations that may not justify their migration cost.`

**Notes or cautions:** Architecture review should distinguish real risks from personal style preferences.

---

### 44. Evaluate modularization options

**Purpose:** Evaluate modularization options.

**When to use it:** When a Spring Boot codebase is becoming difficult to navigate or change safely.

**Complete prompt:**

```text
Evaluate whether the current Spring Boot application would benefit from stronger modular boundaries.

Inspect package dependencies, feature ownership, shared code, transaction boundaries, database ownership, and test structure. Consider options in increasing cost:
- clearer package-by-feature boundaries;
- package visibility and dependency rules;
- Spring Modulith-style modularization if compatible and justified;
- Gradle multi-project modules;
- service extraction only where independent deployment provides real value.

Do not assume microservices are the answer. Identify the actual coupling problems first.

Return a recommended target with rationale, dependency rules, migration sequence, and risks. Prefer the least expensive structure that creates enforceable boundaries. Do not modify code until the target is approved.
```

**Expected AI output:** A cost-aware modularization recommendation tied to concrete coupling problems.

**Optional follow-up prompts:**

- `Design the first module boundary and its allowed dependencies.`
- `Identify shared code that would undermine the proposed module boundaries.`

**Notes or cautions:** Deployment boundaries are much more expensive than code/module boundaries.

---

## Code Reviews

### 45. Perform a senior Spring Boot code review

**Purpose:** Perform a senior Spring Boot code review.

**When to use it:** When reviewing a pull request or selected change set before merge.

**Complete prompt:**

```text
Review the current Spring Boot change set as a senior Java reviewer. Do not rewrite code wholesale.

Prioritize findings that affect:
- correctness;
- transaction semantics;
- concurrency;
- security;
- API compatibility;
- persistence/query behavior;
- error handling;
- observability;
- test quality;
- maintainability.

Use Java 21 and Spring Boot 3.x expectations, but respect established repository conventions. Flag constructor-injection deviations, Jakarta compatibility issues, unnecessary dependencies, entity exposure, or framework misuse only when they materially matter.

For each finding provide:
- severity;
- exact file/location;
- why it matters;
- a concrete minimal fix;
- whether a test should be added or changed.

Also call out strong decisions worth preserving. Do not manufacture issues to fill a quota. End with a merge recommendation: approve, approve with minor changes, or request changes.
```

**Expected AI output:** A concise, severity-ranked review focused on production impact and actionable fixes.

**Optional follow-up prompts:**

- `Review only the tests in this change set for false confidence or missing failure cases.`
- `Re-review after the fixes and report only unresolved issues.`

**Notes or cautions:** Do not turn subjective formatting preferences into blocking review comments.

---

### 46. Review a persistence-heavy change

**Purpose:** Review a persistence-heavy change.

**When to use it:** When a pull request modifies JPA entities, repositories, migrations, or transactional behavior.

**Complete prompt:**

```text
Perform a persistence-focused review of the current change set.

Inspect entity mappings, repositories, migrations, service transaction boundaries, and tests together. Check:
- schema/entity mismatch;
- nullable/unique/foreign-key constraint consistency;
- cascade/orphan semantics;
- fetch behavior and N+1 risk;
- query cardinality and pagination;
- optimistic/pessimistic locking;
- migration safety for existing data;
- transaction rollback behavior;
- backward compatibility during rolling deployment;
- indexes required by new access patterns;
- tests against realistic database behavior.

For each issue, state severity, evidence, likely runtime symptom, and minimal remediation. Do not suggest EAGER fetching as a generic performance fix. Conclude whether the database/application change is safe for production rollout.
```

**Expected AI output:** A production-oriented persistence review spanning code, schema, and rollout behavior.

**Optional follow-up prompts:**

- `Assess whether this migration is safe during a rolling deployment.`
- `Inspect the new query for index support and cardinality risk.`

**Notes or cautions:** Database changes must be reviewed as deployment changes, not just code changes.

---

## Debugging

### 47. Debug a Spring Boot startup failure

**Purpose:** Debug a Spring Boot startup failure.

**When to use it:** When the application context fails to start.

**Complete prompt:**

```text
Diagnose the current Spring Boot startup failure from the available code, configuration, and stack trace/logs.

Work from the first meaningful root cause rather than the final wrapper exception. Trace nested `Caused by` chains and identify:
- the bean/configuration that actually failed;
- whether the cause is missing configuration, dependency mismatch, bean ambiguity/cycle, validation failure, database connectivity, migration failure, or classpath incompatibility;
- why Spring attempted to create that component;
- the smallest fix that preserves intended behavior.

Do not disable validation, exclude auto-configuration, or make beans optional merely to get startup to succeed unless that is architecturally correct.

Apply the minimal fix if the evidence is sufficient. Then rerun the failing startup/test task and the relevant tests. Report root cause, fix, and how the failure can be prevented.
```

**Expected AI output:** A root-cause startup diagnosis with a minimal verified correction.

**Optional follow-up prompts:**

- `Explain the bean-creation chain that led to this failure.`
- `Add a focused context/configuration test that catches this earlier.`

**Notes or cautions:** Spring's outer exception is often only a wrapper; find the deepest relevant cause.

---

### 48. Debug an intermittent production-style failure

**Purpose:** Debug an intermittent production-style failure.

**When to use it:** When a bug is timing-, load-, data-, or environment-dependent and not immediately reproducible.

**Complete prompt:**

```text
Investigate the intermittent failure in this Spring Boot service without guessing.

Build an evidence matrix from available logs, metrics, traces, tests, and code. Identify variables that correlate with failure: request shape, data state, concurrency, instance, dependency latency, transaction timing, pool usage, retries, or deployment version.

Create ranked hypotheses and, for each, specify:
- evidence supporting it;
- evidence against it;
- instrumentation or test needed to confirm/refute it;
- safest short-term mitigation if production impact is high.

Add targeted diagnostic instrumentation or a deterministic reproduction test where possible. Do not add broad retries or exception swallowing as a substitute for diagnosis. Make code changes only after a hypothesis is supported, then run regression tests.
```

**Expected AI output:** A hypothesis-driven investigation that improves observability and avoids speculative fixes.

**Optional follow-up prompts:**

- `Build a deterministic concurrency test for the leading hypothesis.`
- `Add temporary low-volume instrumentation needed to distinguish the top two hypotheses.`

**Notes or cautions:** Intermittent failures often become harder to diagnose when broad retries hide the original signal.

---

## Build Failures

### 49. Diagnose a compile failure after a change

**Purpose:** Diagnose a compile failure after a change.

**When to use it:** When a Java/Spring/Gradle change breaks compilation.

**Complete prompt:**

```text
Diagnose and fix the current Gradle compile failure with the smallest correct change.

Run the failing compile task and start with the first causally relevant compiler error. Determine whether the issue is:
- Java type/signature mismatch;
- missing or wrong import;
- generated-source/annotation-processor failure;
- dependency scope/version issue;
- Java 21 language/toolchain mismatch;
- Jakarta versus javax API mismatch;
- stale code after an API refactor.

Do not apply unrelated cleanup. Fix one root cause at a time and rerun the narrow compile task after each change. Once compilation succeeds, run the relevant tests and then `./gradlew build` if practical.

Report the root cause, files changed, commands run, and whether any remaining warnings deserve action.
```

**Expected AI output:** A minimal compile fix verified by Gradle and followed by relevant tests.

**Optional follow-up prompts:**

- `Explain why the compiler error appeared at this location even if the root cause was elsewhere.`
- `Check whether generated sources or annotation processors are involved.`

**Notes or cautions:** Fix the first root cause before chasing cascaded compiler errors.

---

### 50. Diagnose CI-only Gradle failures

**Purpose:** Diagnose CI-only Gradle failures.

**When to use it:** When the build passes locally but fails in CI.

**Complete prompt:**

```text
Investigate why this Spring Boot Gradle build fails in CI but succeeds locally.

Compare repository-controlled and environment-specific inputs:
- JDK vendor/version and Java toolchain;
- Gradle wrapper usage;
- OS/filesystem case sensitivity;
- locale/timezone;
- environment variables and secrets;
- network/repository access;
- test parallelism;
- Docker/Testcontainers availability;
- cached Gradle state;
- generated files accidentally present locally;
- working-directory assumptions;
- file permissions.

Use CI configuration and logs in the workspace as evidence. Do not “fix” CI by skipping tests or making failures non-fatal.

Reproduce the CI command/environment as closely as possible, identify the smallest deterministic difference, fix it in repository-controlled configuration where appropriate, and rerun relevant tasks. Document any required CI environment contract.
```

**Expected AI output:** A reproducible explanation for the local/CI difference and a durable fix.

**Optional follow-up prompts:**

- `Make the local build use the same Java toolchain as CI.`
- `Identify files or generated artifacts that are present locally but absent from a clean checkout.`

**Notes or cautions:** CI-only failures often expose hidden environmental assumptions that should be removed.

---

## Deployment

### 51. Review containerization for Spring Boot

**Purpose:** Review containerization for Spring Boot.

**When to use it:** When preparing or reviewing a Docker image for a Java 21 Spring Boot service.

**Complete prompt:**

```text
Review the container build for this Java 21 / Spring Boot 3.x service.

Inspect Dockerfiles, Gradle tasks, CI image build, runtime user, base image, JVM options, and deployment manifests.

Evaluate:
- reproducible build/runtime images;
- JDK versus JRE/runtime requirements;
- non-root execution;
- image size without sacrificing maintainability;
- layer caching;
- correct artifact selection;
- JVM container-awareness and memory headroom;
- writable filesystem assumptions;
- signal handling and graceful shutdown;
- health checks versus orchestrator probes;
- secret injection;
- exposed ports and environment configuration;
- image provenance/version pinning appropriate to project policy.

Do not optimize image size at the expense of opaque build complexity. Produce prioritized findings first. Apply approved changes and verify the image/build/startup path where the environment permits.
```

**Expected AI output:** A production-focused container review with pragmatic, verified improvements.

**Optional follow-up prompts:**

- `Refactor the Dockerfile for better dependency-layer caching.`
- `Review JVM memory settings against the container memory limit.`

**Notes or cautions:** Container image concerns and Kubernetes deployment concerns overlap but are not identical.

---

### 52. Review Kubernetes deployment for Spring Boot

**Purpose:** Review Kubernetes deployment for Spring Boot.

**When to use it:** When deploying a Spring Boot service to Kubernetes/EKS.

**Complete prompt:**

```text
Review the Kubernetes deployment artifacts for this Spring Boot service and align them with application behavior.

Inspect Deployment, Service, Ingress, ConfigMaps/Secrets references, autoscaling, probes, resource settings, and rollout strategy.

Evaluate:
- readiness/liveness/startup probes;
- graceful termination and `terminationGracePeriodSeconds`;
- CPU/memory requests and limits;
- JVM memory headroom;
- replica count and disruption behavior;
- HPA metrics and whether the application can scale horizontally;
- session/state assumptions;
- configuration and secret injection;
- rolling-update safety;
- database migration coordination;
- service/ingress ports;
- security context;
- observability labels/metadata.

Do not invent resource numbers without workload evidence. Mark values that require measurement. Recommend application and manifest changes together when they are coupled. Validate manifests with available tooling and run application tests for code changes.
```

**Expected AI output:** A workload-aware Kubernetes deployment review with explicit measurement gaps.

**Optional follow-up prompts:**

- `Check whether graceful shutdown fits the current pod termination window.`
- `Review HPA assumptions for blocking I/O and database connection limits.`

**Notes or cautions:** Scaling pods does not automatically scale downstream database capacity.

---

## General Best Practices

### 53. Modernize Spring Boot code without a rewrite

**Purpose:** Modernize Spring Boot code without a rewrite.

**When to use it:** When a mature codebase needs targeted modernization to Java 21/Spring Boot 3.x practices.

**Complete prompt:**

```text
Identify high-value modernization opportunities in this Spring Boot 3.x / Java 21 repository without proposing a broad rewrite.

Look for changes that materially improve correctness or maintainability, such as:
- constructor injection replacing field injection;
- Java 21 language features where they make code clearer;
- records for appropriate immutable DTO/configuration models;
- Jakarta API consistency;
- type-safe configuration;
- improved transaction boundaries;
- focused tests replacing brittle context-heavy tests;
- removal of obsolete Spring patterns;
- clearer HTTP/error contracts;
- dependency simplification.

Do not change code initially. Rank opportunities by benefit, risk, and migration cost. Exclude cosmetic churn and clever language features that reduce team readability.

After approval, implement one modernization at a time, compile, run tests, and report behavior impact.
```

**Expected AI output:** A prioritized modernization backlog emphasizing maintainability and low-risk value.

**Optional follow-up prompts:**

- `Apply the safest three modernization changes only.`
- `Identify Java 21 features that would *not* improve this codebase and explain why.`

**Notes or cautions:** Modernization should reduce future maintenance cost, not merely make code look newer.

---

### 54. Enforce architectural conventions with tests

**Purpose:** Enforce architectural conventions with tests.

**When to use it:** When important Spring Boot design rules are repeatedly violated in reviews.

**Complete prompt:**

```text
Identify architectural conventions in this repository that are important enough to enforce automatically, then add lightweight architecture tests for the approved rules.

Inspect the current package/module structure and recurring review concerns. Candidate rules may include:
- controllers must not access repositories directly;
- domain packages must not depend on web/infrastructure packages;
- JPA entities must not be exposed from controller methods;
- constructor injection is required;
- package boundaries must follow approved dependencies.

Do not encode subjective style preferences or rules the current architecture cannot satisfy without major migration. Propose a small initial rule set and show current violations before enforcement.

After approval, implement architecture tests using the project's existing test stack or a justified minimal dependency. Make violations actionable. Run the tests and full build.
```

**Expected AI output:** A small set of enforceable architectural guardrails with actionable failures.

**Optional follow-up prompts:**

- `Add only the controller-to-repository dependency rule first.`
- `Create a migration plan for existing violations before making the rule blocking.`

**Notes or cautions:** Architecture tests are valuable only for stable, agreed boundaries.

---
