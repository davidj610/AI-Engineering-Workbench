# 02 -- Model Context Protocol (MCP)

> **Purpose**
>
> Explain what the Model Context Protocol (MCP) is, how it extends AI
> assistants such as Codex, and how to use it safely and effectively in
> a professional software engineering environment.

------------------------------------------------------------------------

# What is MCP?

The **Model Context Protocol (MCP)** is an open protocol that allows an
AI assistant to communicate with external tools and services through a
standard interface.

Instead of being limited to the text in a prompt, an AI can retrieve
information from live systems and perform actions using MCP servers.

Think of MCP as the equivalent of a **device driver layer** for AI
agents.

------------------------------------------------------------------------

# Why MCP Matters

Without MCP, an AI is limited to:

-   Conversation history
-   Files you provide
-   Local repository context

With MCP, an AI can interact with:

-   Databases
-   Source control
-   Docker
-   Kubernetes
-   Cloud providers
-   Documentation systems
-   Internal APIs

This allows the assistant to reason about live systems instead of static
text.

------------------------------------------------------------------------

# High-Level Architecture

``` text
                User
                  │
              Codex CLI
                  │
      ┌───────────┴───────────┐
      │                       │
 Local Shell              MCP Client
      │                       │
      │          ┌────────────┴────────────┐
      │          │            │            │
 Filesystem   PostgreSQL    Docker     GitHub
   Server        Server      Server      Server
```

------------------------------------------------------------------------

# Client vs. Server

## MCP Client

The AI application (for example, Codex CLI).

## MCP Server

A process exposing tools or resources to the AI.

Examples include:

-   Filesystem
-   PostgreSQL
-   Docker
-   Kubernetes
-   AWS

------------------------------------------------------------------------

# Communication Models

## STDIO

The server runs as a local process.

``` bash
codex mcp add filesystem -- \
  npx -y @modelcontextprotocol/server-filesystem \
  /Users/davidjournalist/IdeaProjects
```

## HTTP

The client communicates with a remote MCP endpoint.

------------------------------------------------------------------------

# Managing MCP Servers

List servers:

``` bash
codex mcp list
```

Add a server:

``` bash
codex mcp add filesystem -- \
  npx -y @modelcontextprotocol/server-filesystem \
  /Users/davidjournalist/IdeaProjects
```

Remove a server:

``` bash
codex mcp remove filesystem
```

------------------------------------------------------------------------

# Security Best Practices

-   Grant only the permissions required.
-   Restrict filesystem access to project directories.
-   Avoid exposing your home directory.
-   Protect credentials.
-   Prefer least privilege.

------------------------------------------------------------------------

# Recommended Servers

  Server       Typical Use
  ------------ -------------------------
  Filesystem   Repository awareness
  PostgreSQL   Schema analysis
  GitHub       Repository automation
  Docker       Container inspection
  Kubernetes   Cluster troubleshooting
  AWS          Cloud resource analysis

------------------------------------------------------------------------

# Example Prompts

-   Explain this repository's architecture.
-   Suggest missing database indexes.
-   Why is this Docker container unhealthy?
-   Explain this Kubernetes Deployment failure.
-   Review this IAM policy.

------------------------------------------------------------------------

# Lessons Learned

## Discovery #004

Filesystem MCP transformed Codex into a repository-aware engineering
assistant.

## Discovery #005

Install MCP servers only when they provide capabilities beyond shell
access.

## Discovery #006

Codex CLI includes native MCP management commands.

------------------------------------------------------------------------

# Related Chapters

-   01 -- Codex CLI
-   03 -- Filesystem MCP
-   04 -- GitHub Automation
-   05 -- PostgreSQL MCP
