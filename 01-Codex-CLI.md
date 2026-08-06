# 01 -- Codex CLI

> **Purpose**
>
> Introduce the OpenAI Codex CLI, explain where it fits within a modern
> software engineering workflow, and establish best practices for using
> it effectively in enterprise Java development.

------------------------------------------------------------------------

# Overview

The Codex CLI is an AI-powered engineering assistant that combines large
language model reasoning with direct access to local development tools.

Unlike a traditional chatbot, Codex can:

-   Inspect repositories
-   Read and modify source code
-   Execute shell commands
-   Run builds and tests
-   Use external tools through the Model Context Protocol (MCP)
-   Interact with Git and GitHub
-   Generate documentation
-   Review architecture
-   Automate engineering workflows

The Codex CLI is best viewed as an engineering partner rather than a
command-line replacement.

------------------------------------------------------------------------

# Architecture

``` text
                ChatGPT Account
                       │
               Authentication
                       │
                  Codex CLI
                       │
        ┌──────────────┼──────────────┐
        │              │              │
     Shell         MCP Servers      Skills
        │              │              │
 Git/Gradle      PostgreSQL      OpenAI Docs
 Docker          Docker          Review Agent
 Java            Kubernetes      PDF
```

Codex combines natural language reasoning with local execution
capabilities. The model decides **what** should be done; local tools
perform the actual work.

------------------------------------------------------------------------

# Running Codex

Launch Codex from the root of the repository you want to work on.

``` bash
cd ~/IdeaProjects/MyProject
codex
```

Typical prompts:

-   Explain this repository.
-   Review this architecture.
-   Trace a request through the application.
-   Review this code as a Staff Java engineer.

------------------------------------------------------------------------

# Engineering Tasks

Codex excels at:

-   Repository exploration
-   Architecture reviews
-   Code reviews
-   Refactoring
-   Documentation
-   Build automation
-   Git automation
-   Test generation
-   Security reviews
-   Performance reviews

------------------------------------------------------------------------

# Tasks Better Suited for the Terminal

Use the terminal for mechanical operations:

``` text
pwd
ls
cd
cat
grep
find
git status
docker ps
kubectl get pods
```

These execute almost instantly in a shell but incur AI orchestration
overhead when routed through Codex.

------------------------------------------------------------------------

# Skills

Useful built-in skills include:

-   OpenAI Docs
-   Review Agent
-   PDF
-   Security Best Practices
-   Screenshot

------------------------------------------------------------------------

# Model Context Protocol (MCP)

MCP extends Codex beyond local files.

Typical MCP servers include:

-   Filesystem
-   PostgreSQL
-   Docker
-   Kubernetes
-   AWS
-   GitHub

Grant only the minimum permissions necessary.

------------------------------------------------------------------------

# Recommended Workflow

``` text
Discuss architecture (ChatGPT)
            ↓
Implement (Codex)
            ↓
Build
            ↓
Test
            ↓
Commit
            ↓
Push
```

------------------------------------------------------------------------

# Best Practices

-   Launch Codex from the repository root.
-   Use explicit prompts.
-   Verify generated code.
-   Run builds and tests before committing.
-   Use MCP servers sparingly.

------------------------------------------------------------------------

# Common Pitfalls

-   Using Codex as a shell replacement.
-   Granting excessive filesystem permissions.
-   Skipping verification of generated code.
-   Mixing many unrelated requests into one prompt.

------------------------------------------------------------------------

# Lessons Learned

## Discovery #001

Codex CLI includes first-class MCP management.

``` bash
codex mcp list
codex mcp add
codex mcp remove
```

## Discovery #002

Filesystem MCP dramatically improves repository awareness.

## Discovery #003

Codex excels at repository-scale reasoning but is not intended to
replace the shell for simple commands.

------------------------------------------------------------------------

# Related Chapters

-   02 -- Model Context Protocol
-   03 -- Prompt Library
-   04 -- Filesystem MCP
-   05 -- GitHub Automation
-   06 -- AI Engineering Workflows
