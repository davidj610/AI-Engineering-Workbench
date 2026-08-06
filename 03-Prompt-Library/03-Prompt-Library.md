# 03 -- Prompt Library

> **Purpose**
>
> Build a curated library of reusable prompts for AI-assisted software
> engineering. This chapter is organized by **engineering task**, not by
> AI product, because the same prompt may be useful in ChatGPT, Codex
> CLI, or an IDE assistant depending on the context.

------------------------------------------------------------------------

# A Better Mental Model

Don't think in terms of:

-   ChatGPT prompts
-   Codex prompts
-   IntelliJ prompts

Think in terms of:

-   Learning
-   Repository Analysis
-   Code Editing
-   Architecture
-   Automation
-   Infrastructure
-   Production Support

The same underlying model may power multiple tools, but each environment
exposes different capabilities.

------------------------------------------------------------------------

# Choosing the Right Tool

  ------------------------------------------------------------------------
  Task                Best Tool                           Why
  ------------------- ----------------------------------- ----------------
  Learn a concept     ChatGPT                             Deep
                                                          explanations and
                                                          tradeoff
                                                          discussions

  Review an entire    Codex CLI                           Repository
  repository                                              context, shell,
                                                          Git, MCP

  Refactor the        IntelliJ AI                         Rich editor and
  current class                                           PSI integration

  Build, test,        Codex CLI                           Local tool
  commit, push                                            execution

  Compare             ChatGPT                             Long-form
  architectural                                           reasoning
  options                                                 

  Generate a quick    IntelliJ AI                         Fast inline
  method                                                  assistance
  ------------------------------------------------------------------------

------------------------------------------------------------------------

# Repository Analysis

``` text
Review this repository as though you are a Staff Java Engineer.

Summarize:

- architecture
- technologies
- business flow
- package organization
- technical debt
- recommendations

Do not modify code.
```

------------------------------------------------------------------------

# Codebase Discovery

``` text
I inherited this codebase today.

Create a prioritized learning plan.

Identify:

- entry points
- major workflows
- transaction boundaries
- persistence layer
- deployment model
- testing strategy
```

------------------------------------------------------------------------

# Spring Boot Review

``` text
Review this Spring Boot application.

Evaluate:

- package structure
- dependency injection
- REST API design
- transaction boundaries
- exception handling
- configuration
- testing

Rank findings by impact.
```

------------------------------------------------------------------------

# Persistence

``` text
Review all JPA entities.

Identify:

- N+1 risks
- fetch strategy issues
- cascade problems
- optimistic locking usage
- aggregate boundaries
```

------------------------------------------------------------------------

# Performance

``` text
Profile this application.

Identify:

- CPU bottlenecks
- blocking operations
- synchronization
- unnecessary allocations
- caching opportunities

Estimate expected benefit for each recommendation.
```

------------------------------------------------------------------------

# Docker & Kubernetes

``` text
Review these deployment artifacts.

Evaluate:

- production readiness
- security
- image optimization
- health probes
- resource requests and limits
- configuration management
```

------------------------------------------------------------------------

# AWS

``` text
Review this AWS architecture.

Evaluate:

- scalability
- resilience
- security
- cost
- observability

Explain tradeoffs for every recommendation.
```

------------------------------------------------------------------------

# Production Readiness

``` text
Perform a production readiness review.

Evaluate:

- logging
- monitoring
- configuration
- security
- deployment
- rollback strategy
- testing
- documentation
```

------------------------------------------------------------------------

# AI Workflow Prompts

## Before Coding

``` text
Explain the problem first.

Propose two or three implementation approaches.

Discuss tradeoffs before writing code.
```

## Before Commit

``` text
Review today's changes.

Identify:

- bugs
- technical debt
- missing tests
- documentation gaps
- cleanup opportunities

Do not modify code until the review is complete.
```

------------------------------------------------------------------------

# Prompt Design Principles

A strong engineering prompt should define:

1.  **Role** -- Who should the AI act as?
2.  **Objective** -- What problem should it solve?
3.  **Context** -- What information is available?
4.  **Constraints** -- What should it avoid?
5.  **Output** -- What should it produce?

Treat prompts as reusable engineering assets and refine them over time.

------------------------------------------------------------------------

# Lessons Learned

## Discovery #007

Organize prompts by engineering task rather than AI product.

## Discovery #008

Repository-scale tasks belong in Codex CLI.

## Discovery #009

Editor-scale tasks belong in IntelliJ AI.

## Discovery #010

Architecture and tradeoff discussions are often best handled in ChatGPT
before implementation begins.

------------------------------------------------------------------------

# Related Chapters

-   01 -- Codex CLI
-   02 -- Model Context Protocol
-   04 -- Filesystem MCP
-   05 -- GitHub Automation
-   06 -- AI Engineering Workflows
