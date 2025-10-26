---
date: 2025-08-29
updated: 2025-09-22
---
# Multi-Context Protocol

## Overview

```mermaid
---
config:
  layout: dagre
  look: handDrawn
  theme: classic
---
graph TD
  user("User")
  client1["MCP Client 1"]
  client2["MCP Client 2"]
  host["MCP Host"]
  llm["Client LLM<br>e.g. gemini, copilot"]
  mcp1c["Controller"]
  mcp2["MCP Server 2"]
  mcp_llm["MCP Server's LLM"]

  subgraph "Client App<br>e.g. vscode"
    host --> client1
    host --> client2
  end

  subgraph "MCP Server 1"
    mcp1c --resources/list<br>resources/read--> resources
    mcp1c --tools/list<br>tools/call--> tools
    mcp1c --prompts/list<br>prompts/get--> prompts
  end

  user --> host
  host --prompt<br>tool catalog--> llm
  llm --request<br>tool execution--> host
  client1 --mcp primitives: resources/tools/prompts<br>negotiate capabilities--> mcp1c
  client2 --mcp--> mcp2
  mcp2 --sampling/complete<br>elicitation/request--> client2
  mcp2 --> mcp_llm
```

- **MCP Host** can be standalone or embedded in another app / service (e.g. IDE, LLM UI, your in-house enterprise AI platform)
  - The Host is also what talks to the LLM (the model not the service) on your behalf.
- **MCP Clients** are spawned by the MCP Host to communicate with MCP servers. Typically automatically provisioned and managed by the Host app.
- What you do as an end-user is to configure your Host to talk to various MCP servers of your choice.
- **MCP servers** may run locally on your machine, on a server you manage, or provided by a commercial entity.

Note that:

- The LLM context will be much larger than simply prompting, since the LLM can request for more info without user input.
- Your data will still be sent to the LLM by the Host as part of its context. No special magic there to do otherwise. Safeguards against data leaks is the Host’s responsibility.

## Building MCP services

### Security

- Protecting end-user credentials and resoources access firstly is on the Host.
- The MCP services will need to validate user auth tokens (is this standardised?). The Host will need to know what auth tokens to store and how to send it to the MCP services, and inform/re-auth the user if necessary should access be denied.
- Possibly MCP servers meant to run locally will not implement any auth, and potentially has free access to your machine. It is possible for a malicious local MCP server to attack remote machines through yours. Be careful what you are running locally.

### Caching

- Similar to APIs, you may want to cache API responses to reduce network calls and improve performance.
