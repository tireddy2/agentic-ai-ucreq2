---
title: "Agentic AI Use Cases and Requirements"
abbrev: "agentic-ai-ucreq"
docname: draft-agentic-ai-usecases-requirements-latest-00
category: info
ipr: trust200902
submissiontype: IETF
keyword: Internet-Draft

stand_alone: yes
pi:
  toc: yes
  sortrefs: yes
  symrefs: yes

author:
 -
    name: Tirumaleswar Reddy
    organization: Nokia
    city: Bangalore
    region: Karnataka
    country: India
    email: "kondtir@gmail.com"
 -
    ins: Z. Sarker
    name: Zaheduzzaman Sarker
    organization: Nokia
    country: Sweden
    email: zaheduzzaman.sarker@nokia.com
 -
    name: Kehan Yao
    organization: China Mobile
    email: yaokehan@chinamobile.com
-
    name: Parisa Foroughi
    organization: Nokia
    email: parisa.foroughi@nokia.com

informative:
  A2A:
    title: "Agent2Agent Protocol Specification"
    target: https://a2a-protocol.org/latest/specification/

  MCP:
    title: "Model Context Protocol Specification"
    target: https://modelcontextprotocol.io/specification/2025-11-25

  SCRM:
    title: "Agentic AI Use Cases"
    target: https://datatracker.ietf.org/doc/draft-scrm-aiproto-usecases

  ROSENBERG:
    title: "Framework, Use Cases and Requirements for AI Agent Protocols"
    target: https://datatracker.ietf.org/doc/draft-rosenberg-aiproto-framework

  YAO:
    title: "Problem Space Analysis of AI Agent Protocols in IETF"
    target: https://datatracker.ietf.org/doc/draft-yao-catalist-problem-space-analysis

  SONG:
    title: "Problem Statement and Requirements for Dynamic Multi-agent Secured Collaboration"
    target: https://datatracker.ietf.org/doc/draft-song-dmsc-problem-statement

  RFC7696:
    title: "Guidelines for Cryptographic Algorithm Agility and Selecting Mandatory-to-Implement Algorithms"
    target: https://www.rfc-editor.org/rfc/rfc7696

  KLRC:
    title: "AI Agent Authentication and Authorization"
    target: https://datatracker.ietf.org/doc/draft-klrc-aiagent-auth

  AUTOGEN:
    title: "AutoGen: A Framework for Multi-Agent Conversation"
    target: https://microsoft.github.io/autogen/stable/

  LANGCHAIN:
    title: "LangChain Agent Framework"
    target: https://python.langchain.com/docs/concepts/agents/

  OPENAI-AGENTS:
    title: "OpenAI Agents SDK"
    target: https://openai.github.io/openai-agents-python/

--- abstract

This document describes use cases for agentic AI communication systems
and derives protocol requirements from those use cases. The requirements
are intended to guide IETF standardization work on protocols in the
context of agent-to-agent communication, agent-to-tool communication,
with focus on multimodal communication, session management, discovery,
communication security, agent identity and authentication.

--- middle

# Introduction

An AI agent is an autonomous, adaptive intelligent software system
that uses AI models to complete a specific objective on behalf of
a user or another AI agent. It makes decisions, executes actions,
and interacts with other agents through tasks and tools. Unlike
traditional software workloads that follow fixed execution paths, an
AI agent dynamically determines at run time which actions to take,
which tools to invoke, and which agents to collaborate with, based on
reasoning over its goals and context.

This document presents use cases that illustrate the key interaction
patterns of agentic AI communication systems, and derives protocol
requirements from those use cases. The requirements are intended to
drive development of protocols and a protocol framework for agentic AI systems.

The use cases in this document cover interaction patterns for
agentic AI communication systems. This document takes into account
related use case and problem statement documents including [SCRM],
[YAO], [SONG], and [ROSENBERG], and existing protocol work including
[A2A] and [MCP].

# Terminology {#terminology}

**AI Agent**: An autonomous software entity that perceives its
environment, maintains internal state, and executes actions to achieve
specified goals, potentially including communication with other agents
or invocation of external tools.

**Agent Identity**: Identity information associated with an AI agent,
used for authentication and accountability within agentic communication
systems.

**Agentic AI Communication System**: A system comprising one or more
AI agents that communicate with each other, with users, and with
external tools or services to complete tasks. The communication
interfaces between these entities are the subject of protocol
standardization in this document.

**Agent-to-Agent Communication**: Direct or brokered
communication between two or more AI agents, where brokered
communication involves an intermediary agent or coordination service,
as distinguished from communication between an agent and a user or
between an agent and a tool.

**Capability**: A description of what an agent can
perform, including inputs, outputs, constraints, and required conditions.

**Context**: The set of data, state, and history shared between agents
to enable task execution and coordination.

**Coordinator Agent**: An agent that distributes a shared problem or task
to a group of peer agents, aggregates their outputs, and iteratively drives
them toward a collective result or consensus.

**Delegation**: The act of an agent requesting another agent to execute
a task on its behalf.

**Initiating Agent**: An agent that receives an initial request and
delegates subtasks to peer agents. Any peer agent may itself delegate
further to other agents without routing through the initiating agent.

**Mediator**: An entity that serves as a proxy or mediator for
external tools, APIs, databases, or other resources that other agents
require but cannot directly access.

**Message**: A discrete unit of communication exchanged between agents
containing structured data such as a task request, response, progress
update, event notification, or control signal.

**Modality**: A category of data format used for input or output in
agent communication, such as text, audio, image, or video. A session
may support one or more modalities simultaneously.

**Orchestrator Agent**: An agent that acts as a controller,
coordinating the activity of other agents by decomposing goals into
sub-tasks and delegating those sub-tasks to appropriate agents.

**Peer Agent**: An agent that receives delegated subtasks from another
agent and may itself delegate further to other agents.

**Session**: A logical communication exchange shared between two or more
agents over a period of time, which may persist across multiple
individual message exchanges and network connections. A session can carry
and maintain one or more contexts shared between agents.

**Task**: A unit of work submitted by a user to an agent, or
delegated by one agent to another.

**Task State**: The current execution status of a task (e.g., pending,
in-progress, completed, failed).

**Tool**: An external service invoked by an agent to retrieve data or
perform operations. A tool is not necessarily an agent and may not participate
in agent-to-agent communication.

**User**: A human that initiates interaction with an
AI agent by submitting a request or task.

# Common Protocol Requirements {#common-requirements}

The following baseline requirements apply to both agent-to-agent and agent-to-tool protocol interactions across all use cases and are not repeated per use case.

Each per-use-case requirement is tagged with one or more of the following protocol area tags to allow cross-use-case navigation:

- **Discovery**: Requirements related to locating, advertising, or selecting agents, tools, or capabilities.
- **Transport**: Requirements related to message delivery, streaming, cancellation, session management, and data transfer.
- **Security**: Requirements related to confidentiality, integrity, and authorization.
- **Authentication**: Requirements related to identity verification and credential delegation.

| REQ-ID | Description | Tag |
|--------|-------------|-----|
| CMN-1  | The protocol is required to support any client application to communicate with any agent service. | Discovery, Authentication |
| CMN-2  | Mutual authentication is required between all communicating parties. | Authentication |
| CMN-3  | All protocol traffic is required to be encrypted and integrity-protected in transit. | Security |
| CMN-4  | Structured error responses are required to include an authorization scope violation type, reported by the orchestrator or mediator when an agent attempts an action that exceeds or contradicts the scope delegated to it. | Security |
| CMN-5  | Structured error responses are required, distinguishing at minimum: authentication failure, authorization failure, timeout, and internal error. | Transport |
| CMN-6  | The protocol is required to provide a means to signal task priority so that critical-path tasks can be scheduled ahead of lower-priority ones. | Transport |
| CMN-7  | The protocol is required to support cryptographic algorithm agility, ensuring that cryptographic algorithms used for encryption, authentication, credential verification, and integrity protection can be negotiated and updated over time, in accordance with {{RFC7696}}. | Security, Authentication |
| CMN-8  | The protocol is required to provide a means to verify the agent authentication credentials validity used by agents at the time of use. | Authentication |
| CMN-9  | The protocol is required to support signaling credential revocation and invalid credential outcomes. | Security |

# Use Cases {#usecases}

## Simple Single-Agent Task {#simple-single-agent}

### Description

A user submits a task to an AI agent via a client application. The
agent executes the task by invoking one or more tools and returns
results to the user. The tools invoked by the agent may reside in
the same or a different administrative domain. The agent protocol
is required to support multiple input and output modalities, and
the client application and agent are required to be able to negotiate which
modalities are active for the session.

This use case covers the protocol interface between the client
application and the agent. The interaction between the user and
the client application is out of scope. This use case assumes that
the user communicates with the agent via a client application;
direct communication between a user and an agent without an
intermediary client application is not covered in this use case.

This interaction pattern is described in [ROSENBERG] and [SCRM].

### Interaction Flow

~~~
+----------------+                       +-----------+
| App/agent      |<--------------------> |   Agent   |
+----------------+        Protocol       +-----------+
                                               |
                                     Protocol  |
                                               v
                                          +---------+
                                          | Tool(s) |
                                          +---------+
~~~

### Protocol Requirements {#a1-protocol-requirements}

| REQ-ID | Description | Tag |
|--------|-------------|-----|
| A1-1  | The protocol is required to support incremental streaming of agent output, allowing partial results to be delivered to the client before the agent has completed processing. | Transport |
| A1-2  | The protocol is required to define a task cancellation message that the client can issue at any point during task execution. | Transport |
| A1-3  | The protocol is required to define structured error message types that distinguish at minimum: transport failure, tool invocation failure, and agent processing failure. | Transport |
| A1-4  | The protocol is required to support multiple modalities for both input and output. | Transport |
| A1-5  | The protocol is required to support modality negotiation at session setup, allowing the client and agent to agree on which modalities are active for the session. | Discovery, Transport |
| A1-6  | The protocol is required to support agent-initiated notifications to the client during task execution. | Transport |
| A1-7  | The protocol is required to support concurrent invocation of multiple tools within a single agent task, where tools may be operated by distinct providers across different administrative domains, each with independent authentication and authorization requirements. | Discovery, Transport, Security |
| A1-8  | The protocol is required to support bulk transfer of large data between communicating parties, applicable to both agent-to-tool and agent-to-agent interactions. | Transport |
| A1-9 | A delegation mechanism is required to be defined by which an agent presents to a tool provider a credential attesting the authorization for the requested tool access, without exposing the client's primary credentials. | Authentication |

## Orchestrator and Agent Collaboration {#orchestrator-agent}

### Description

An orchestrator agent acts as a controller, decomposing a task into
subtasks and delegating them asynchronously to one or more other agents.
The orchestrator decides which other agents to invoke, sequences the
delegation, and aggregates results to continue task execution. Each
agent executes the respective subtask independently and reports results back
to the orchestrator.

It should be noted that AI models are stateless by nature — each inference
call processes only what is explicitly provided with a particular context,
with no persistent memory between calls. The application
layer is responsible for maintaining the context across the calls by
carrying conversation history, intermediate results, and task
state. Session continuity is therefore required to preserve this
accumulated context across network interruptions, ensuring that a
reconnecting agent can restore the prior task context without
having to reconstruct it from scratch.

This pattern is described in [ROSENBERG] and reflected in [A2A],
and is implemented in deployed multi-agent frameworks including
[AUTOGEN], [LANGCHAIN], and [OPENAI-AGENTS].

### Interaction Flow

~~~
+---------------------+                        +------------+
| Orchestrator Agent  |---Task Delegation----->|   Agent-1  |
|                     |<--Result Reporting-----|            |
|                     |                        +------------+
|                     |
|                     |                        +------------+
|                     |---Task Delegation----->|   Agent-2  |
|                     |<--Result Reporting-----|            |
+---------------------+                        +------------+
~~~

### Protocol Requirements {#b1-protocol-requirements}

| REQ-ID | Description | Tag |
|--------|-------------|-----|
| B1-1  | The protocol is required to facilitate task delegation for an orchestrator to agents that includes task delegation and acknowledgement message types. | Transport |
| B1-2  | The protocol is required to support asynchronous delegation, allowing the orchestrator to delegate to multiple agents without waiting for each to complete before proceeding. | Transport |
| B1-3  | The protocol is required to define a result reporting message by which an agent returns its completed output to the orchestrator. | Transport |
| B1-4  | The protocol is required to support streaming of intermediate results from the agent to the orchestrator during task execution. | Transport |
| B1-5  | The protocol is required to define a task cancellation message that the orchestrator can send to an agent to abort a delegated subtask. | Transport |
| B1-6  | The protocol is required to support persistent session identifiers that survive network interruption, and is required to define a session resumption message by which an agent re-attaches to an interrupted session restoring the prior task context. | Transport |

## Long-Running Delegated Task with Authorization Checkpoint {#authz-checkpoint}

### Description

An orchestrator agent delegates a long-running task to other agents.
The delegated agent executes the task autonomously and sends progress
notifications to the orchestrator. At any certain point one or more delegated
agents pause and request explicit authorization from the orchestrator
before proceeding further. The orchestrator may relay this authorization
request to the invoker (user or agent) or resolve it autonomously based on policy.

This pattern is reflected in the In-Task Authorization mechanism
defined in [A2A].

### Interaction Flow

~~~
+---------------------+                        +------------+
| Orchestrator Agent  |---Task Delegation----->|            |
|                     |<--Progress Notif.------|            |
|                     |<--Authz Checkpoint-----|  Agent(s)  |
|                     |---Authz Response------>|            |
|                     |<--Result Reporting-----|            |
+---------------------+                        +------------+
~~~

### Additional Protocol Requirements {#b2-protocol-requirements}

This use case builds on the requirements defined for {{orchestrator-agent}}
and introduces additional requirements specific to authorization checkpoints in delegated tasks.

| REQ-ID | Description | Tag |
|--------|-------------|-----|
| B2-1  | The protocol is required to support agent-initiated progress notifications to the delegating agent during task execution. | Transport |
| B2-2  | The protocol is required to define an authorization checkpoint message by which an agent pauses task execution and requests explicit authorization from the orchestrator before proceeding. The message is required to include sufficient context for the authorizing party to make an informed decision, including the action to be taken and its potential consequences. | Transport, Security |
| B2-3  | The protocol is required to define the valid responses to an authorization checkpoint, including at minimum: approve, deny, and approve with modified parameters. A denial is required to be conveyed as an explicit error response. |  Transport, Security |
| B2-4  | The protocol is required to support a response timeout, after which the agent treats the request as unresolved and halts the affected subtask. |  Transport |

## Peer Collaborative Multi-Agent Problem Solving {#peer-collaborative}

### Description

A task requires coordinated problem solving across multiple agents,
where no single agent has full authority or capability to complete
the task alone. The agent that receives the initial request
dynamically delegates subtasks to peer agents based on their
advertised capabilities. Any agent may itself delegate further to
other agents and use other tools, forming a dynamic collaboration
graph. Each agent remains opaque to others, collaborating only
through the protocol interface.

This use case introduces multi-hop delegation chains that are not
present in {{orchestrator-agent}}. Each agent in the chain may
delegate further to other agents, and authorization scope is required
to be progressively constrained at each hop.

This use case is described in [A2A] and [ROSENBERG].

### Interaction Flow

~~~
  +------------------+
  | Initiating Agent |
  +------------------+
      |         |
      v         v
 +--------+  +--------+
 |Agent-2 |  |Agent-3 |
 +--------+  +--------+
      |          |
      v          v
 +--------+  +--------+
 |Agent-4 |  | Tools  |
 +--------+  +--------+
~~~

### Additional Protocol Requirements {#b3-protocol-requirements}

This use case builds on the requirements defined for {{orchestrator-agent}}
and introduces additional requirements specific to multi-hop delegation chains.

| REQ-ID | Description | Tag |
|--------|-------------|-----|
| B3-1  | The protocol is required to support multi-hop delegation chains, where an agent that receives a delegated subtask may itself delegate further to other agents. At each hop, the delegating agent is required to present a credential that does not exceed the authorization scope of the credential it received. | Authentication, Security |
| B3-2  | The protocol is required to preserve the identity of the originating entity across all hops in the delegation chain, such that any agent in the chain can determine the identity of the entity that originally authorized the task. | Authentication, Security |
| B3-3  | The protocol is required to support transferable credentials that carry the original authorization constraints across all hops in the delegation chain. Each receiving agent is required to be able to cryptographically verify that the credential presented to it was issued by the delegating agent and that the chain of delegation traces back to the original authorization. | Authentication, Security |
| B3-4  | The protocol is required to ensure that authorization granted to an agent in a delegation chain, including for tool invocations, is derived from the authorization issued by the initiating agent, and not from the identity or authorization scope of any intermediate agent in the chain. | Authentication, Security |
| B3-5  | The protocol is required to define a capability registration mechanism by which agents can publish metadata describing their capabilities, supported protocols, rate limits, authentication methods, authorization mechanisms, and authorization scopes to a discovery service or registry. | Registration, Discovery |
| B3-6  | The protocol is required to define a capability discovery mechanism by which agents can query a discovery service or registry to discover and select appropriate peer agents at runtime without requiring prior peer-specific configuration. | Discovery |
| B3-7  | The protocol is required to define an agent identifier format that uniquely represents an agent identity and is resolvable to the agent's communication endpoint using an appropriate discovery mechanism. | Discovery |
| B3-8  | The protocol is required to ensure that advertised capabilities are integrity-protected, such that a discovering agent can verify they have not been tampered with. | Discovery, Security |

## Cooperative Reasoning and Consensus Formation {#cooperative-reasoning}

### Description

A set of peer agents is tasked with analyzing a shared problem
independently and exchanging intermediate reasoning outputs to
converge on a collective conclusion. A coordinator agent distributes
the problem to all participating agents, collects their reasoning
outputs, and drives the convergence process across multiple rounds
until a consensus conclusion is reached. Unlike
{{peer-collaborative}}, all agents work on the same problem rather
than different subtasks.

Two communication topologies are possible. In the first, agents
communicate only through the coordinator, which acts as the central
hub for all message exchange. In the second, agents may also
communicate directly with each other to exchange intermediate
reasoning outputs without routing through the coordinator agent. The
second topology introduces the same multi-hop authorization
requirements defined in {{peer-collaborative}}.

### Interaction Flow

The coordinator-mediated topology:

~~~
                +--------------------+
                | Coordinator agent  |
                +--------------------+
                  /      |      \
                 v       v       v
           +--------+ +--------+ +--------+
           |Agent-1 | |Agent-2 | |Agent-3 |
           +--------+ +--------+ +--------+
~~~

The direct agent-to-agent topology:

~~~
                 +--------------------+
                 | Coordinator agent  |
                 +--------------------+
                 /          |         \
                v           v          v
           +-------+     +-------+     +-------+
           |Agent-1| <-> |Agent-2| <-> |Agent-3|
           +-------+     +-------+     +-------+
               ^                           ^
               |___________________________|
~~~

### Protocol Requirements

This use case builds on the requirements defined for {{orchestrator-agent}}
and introduces additional requirements specific to group message delivery.

### Additional Protocol Requirements {#b4-protocol-requirements}

| REQ-ID | Description | Tag |
|--------|-------------|-----|
| B4-1  | The protocol is required to support one-to-one, one-to-many, and many-to-many message delivery among a defined group of agents. Group membership is required to be dynamic, allowing agents to join or leave the group during the course of an exchange. | Transport, Security |

## Tool, Data, and API Mediation Between Agents {#tool-mediation}

### Description

In many multi-agent deployments, access to external resources —
APIs, databases, enterprise systems, or hardware interfaces — is
intentionally mediated through a designated mediator. Other agents
request the mediator to perform actions or retrieve data on their
behalf, rather than directly invoking external systems. This
architecture allows access control, auditing, rate limiting, and
schema normalization to be applied uniformly at the mediation layer.

The mediator may also serve as an adapter between the agent
protocol and non-agent systems or other services that do not natively
support agent communication protocols, or between different agent
communication protocols such as translating between the agentic
protocol suite being developed at the IETF and existing protocols
such as [MCP] and [A2A]. In this role, the mediator is responsible
for protocol translation and for presenting the appropriate
credentials to the target system on behalf of the requesting agent.

The mediator may additionally act as a request router, dispatching requests
to appropriate agents or tools based on the content and context of
the request, without requiring the requesting agent to have prior
knowledge of which agent or tool is most appropriate.

The mediator may also validate agent requests before invocation,
checking whether the action being requested matches the
authorization granted to the agent and whether execution would
cause unintended or irreversible side effects.

Note that mediating can be a function within an orchestrator.

This pattern is reflected in the MCP server architecture defined
in [MCP] and the agent routing patterns discussed in [A2A].

### Interaction Flow

~~~
       +--------------+
       |    Agent     |
       +--------------+
              |
              v
       +--------------+
       |   Mediator   |
       +--------------+
       /       |       \
      v        v        v
+--------+ +---------+  +--------+
| Agent-1 | | Tool   |  | Agent-2 |
+--------+ +---------+  +--------+
~~~

### Additional Protocol Requirements {#b5-protocol-requirements}

| REQ-ID | Description | Tag |
|--------|-------------|-----|
| B5-1  | The protocol is required to define error response types for request validation failure (rejected due to potential unintended or irreversible side effects) and protocol translation failure (rejected on unsuccessful translation of a request or response between supported protocols), distinct from authorization failure. | Transport, Security |
| B5-2  | The protocol is required to support exchange of structured (audit) record for each action performed on behalf of a requesting agent, including the requesting agent's identity, the authorization credential presented, the action taken, and the outcome. | Security |


## Dimensional Model of Use Cases {#dimensional-model}

This section introduces a dimensional model for characterising the
use cases of {{usecases}} along a small number of orthogonal,
protocol-visible properties. The model is intended to make the
use cases comparable on operational grounds, to surface the
protocol primitives each value of each dimension implies, and to
provide a framework in which extensions and deployment-context
modifiers can be discussed without enlarging the base model
itself.

The model is offered as a tool for analysis and discussion. It is
not normative. Where the analysis surfaces design choices that
the present document does not explicitly resolve, those choices
are recorded as open questions for further work rather than as
findings.

### The Protocol's Substrate {#substrate}

The dimensions in this section are properties of agent-protocol
exchanges. Their values are not all satisfied by the agent
protocol itself: several are sourced from layers adjacent to it.
Collectively these adjacent layers are referred to as the
protocol's substrate, and include:

* Transport: how bytes are moved, how connections persist, how
  callee-initiated delivery is supported, and how reattachment
  after interruption is handled.

* Identity and authorization: how credentials are issued,
  narrowed, carried across hops, and verified.

* Discovery: how endpoints and their advertised capabilities are
  located.

* Audit: how actions taken on behalf of others are recorded for
  accountability.

* Security: how confidentiality, integrity, and group trust are
  established for the exchange.

The protocol agenda this section informs is therefore the
specification of (a) the agent-protocol semantic surface (the
dimensions and their values), (b) the substrates the protocol
binds to in each facet, and (c) the integration points between
the agent protocol and each substrate. Where existing standards
provide the needed substrate behaviour in a given facet, the path
forward is binding specification or profile, not protocol
invention. Where they do not, the gap is named explicitly.

### Dimensions {#dimensions}

The model identifies seven dimensions. Each dimension is defined
over a small value domain. A use case is described by selecting
one value from each dimension, together with zero or more
extensions ({{extensions}}) and an optional deployment-context
modifier ({{cross-domain-modifier}}). Additional dimensions were considered
and set aside; these are recorded in {{dimensions-not-adopted}} to mark the
boundary of the adopted model.

#### D1 Initiation Rights {#d1-initiation-rights}

Which party is permitted to originate a message within an
established association. Values:

* caller-only: only the party that opened the association may
  send requests.

* callee-may-initiate: the callee may originate messages back to
  the caller after the association is established, supporting
  streamed events, push notifications, and pause-or-resume
  signalling.

All use cases in {{usecases}} require callee-may-initiate at minimum,
because each use case involves responder-originated messages such
as streamed intermediate results, agent-initiated notifications,
authorization checkpoints, or audit emissions.

#### D2 Output Cadence {#d2-output-cadence}

The temporal shape of a task's output. Values:

* atomic: a single request yields a single response.

* streamed: a single request yields a sequence of partial outputs
  over time.

Use cases in {{usecases}} use streamed at the agent-to-agent
interface; atomic is a degenerate case present at the substrate
layer (for example, simple tool calls) but is not exercised as a
use case in this document.

#### D3 Lifecycle Control {#d3-lifecycle-control}

Whether and how a task may pause or persist beyond a single
connection. Values:

* none: the task runs to completion within the originating
  exchange without pause or reattachment.

* suspend-resume: the task may pause awaiting an out-of-band
  input (such as an authorization decision) and resume.

* durable-reattach: the task survives transport interruption and
  may be reattached using a persistent task identifier.

{{authz-checkpoint}} motivates suspend-resume. {{cooperative-reasoning}} motivates
durable-reattach when the protocol exposes a persistent task
identifier; an alternative realisation maintains state at the
application layer with repeated independent exchanges, in which
case D3 is none and the multi-round structure is invisible at the
protocol layer.

#### D4 Authorization Derivation {#d4-authorization-derivation}

The source of the authority under which an agent acts. Values:

* direct: the agent acts under authority granted directly to it
  by the request originator.

* derived-1hop: the agent acts under authority derived from a
  single prior authorization, with verifiable derivation and
  scope narrowing.

* derived-chain: the agent acts under authority derived through
  two or more derivation steps, each cryptographically verifiable
  and progressively narrowed back to the originator.

* local-policy: the agent acts under its own standing authority
  granted by its operator, applying local rules upon
  authentication of the requester.

{{simple-single-agent}} (A1-9) motivates derived-1hop for tool access.
{{peer-collaborative}} (B3-1, B3-3, B3-4) motivates derived-chain.
{{tool-mediation}}'s mediator may operate either as derived (preserving
the originator's authorization chain to downstream systems) or as
local-policy (terminating the chain at the mediator and presenting
its own credentials downstream). The present document does not
resolve this choice; it may benefit from explicit treatment in a
future revision.

#### D5 Endpoint Binding {#d5-endpoint-binding}

How the calling party comes to know the addressable identifier of
its counterpart. Values:

* pre-bound: the endpoint is known to the caller before the
  exchange begins, by configuration or out-of-band agreement.

* registry-resolved: the caller queries a third-party registry
  at runtime to obtain the endpoint and its advertised
  capabilities.

Most current use of agentic protocols proceeds pre-bound, with
per-agent capability self-description published at a known
endpoint (the well-known URI pattern). {{peer-collaborative}} (B3-5 through
B3-8) is the use case most explicitly requiring registry-resolved
binding. The mediator pattern of {{tool-mediation}} may itself absorb
the discovery responsibility, performing registry-resolved
dispatch on behalf of pre-bound callers.

#### D6 State Locality {#d6-state-locality}

Where the state required to continue a task across messages is
maintained. Values:

* caller-held: the caller carries the necessary context on each
  message; the callee is stateless across messages.

* callee-held: the callee maintains task state keyed by an
  identifier; the caller references the state by that
  identifier.

The use cases in {{usecases}} implicitly assume callee-held, which
combines naturally with D3 durable-reattach. A caller-held
alternative is realised by REST-style and tool-call-oriented
protocols and is not foreclosed by the present requirements;
recording the choice avoids inadvertent foreclosure.

#### D7 Result Addressability {#d7-result-addressability}

How the output of a task is exposed. Values:

* inline-only: the result is contained in the response.

* referenceable-artifact: the result is produced as a named
  artifact with an identifier; the artifact can be fetched,
  shared with a third party, or referenced in subsequent calls,
  and may outlive the producing task.

The use cases in {{usecases}} are written assuming inline-only.
Long-running consensus ({{cooperative-reasoning}}) and audit records ({{tool-mediation}})
are natural candidates for referenceable-artifact semantics;
the choice is left to subsequent work.

### Mapping of Use Cases to Dimensions {#use-case-mapping}

The following table summarises the dimensional value selected by
each use case in {{usecases}}. Where a use case does not explicitly
constrain a dimension, the inherited or implicit value is given
in parentheses. Ambiguous entries are marked.

| Use Case | D1 | D2 | D3 | D4 | D5 |
|----------|----|----|----|----|-----|
| {{simple-single-agent}} | C-I | str. | none | direct + derived-1hop (tool, A1-9) | pre-bound |
| {{orchestrator-agent}} | C-I | str. | (none) | (local-policy) | pre-bound |
| {{authz-checkpoint}} | C-I | str. | suspend-resume | (local-policy) | pre-bound |
| {{peer-collaborative}} | C-I | str. | (none) | derived-chain | registry-resolved |
| {{cooperative-reasoning}} | C-I | str. | durable-reattach | (direct or local-policy) | pre-bound |
| {{tool-mediation}} | C-I | str. | (none) | ambiguous: derived-1hop or local-policy | pre-bound (mediator resolves downstream) |

C-I = callee-may-initiate; str. = streamed

All use cases use D6 callee-held implicitly. D7 is inline-only
across all use cases as currently written.

### Extensions {#extensions}

An extension augments the baseline dimensional tuple of a use case
with primitives the baseline does not require. Extensions are
used when an additional behaviour does not warrant a new
dimension value but does require a named protocol augmentation.

The following extensions are identified from the use cases of
{{usecases}}:

* EXT-CHKPT: authorization checkpoint primitives (pause, resume,
  timeout) supporting D3 suspend-resume. Motivated by {{authz-checkpoint}}.

* EXT-REATTACH: persistent task identifier and reattachment
  message supporting D3 durable-reattach. Motivated by {{cooperative-reasoning}}
  when realised at the protocol layer.

* EXT-CAPREG: capability registration and discovery messages,
  with integrity protection of advertised capabilities,
  supporting D5 registry-resolved. Motivated by {{peer-collaborative}}
  (B3-5 through B3-8).

* EXT-AUDIT: structured audit record exchange describing actions
  taken on behalf of requesting parties. Motivated by {{tool-mediation}}
  (B5-2).

* EXT-XLATE: error vocabulary for protocol translation failure
  and request validation failure, distinct from authorization
  failure. Motivated by {{tool-mediation}} (B5-1).

* EXT-MODNEG: modality negotiation at session setup, supporting
  multimodal exchanges where the active modalities are agreed
  between the client and the agent. Motivated by {{simple-single-agent}}
  (A1-4, A1-5).

### Deployment-Context Modifier: Cross-Domain Operation {#cross-domain-modifier}

Cross-administrative-domain deployment is not modelled as a
dimension. It is a property of how a use case is deployed and it
tightens the practical value sets on existing dimensions:

* D4: local-policy becomes insufficient where the receiver does
  not recognise the caller's operator as authoritative for the
  receiver's resources; direct or derived authorization is
  required.

* D5: pre-bound binding does not scale across organisations;
  federated registry-resolved discovery becomes attractive.

* D4 (chain depth): transitive delegation across organisations
  places the strongest demands on credential chainability,
  pointing to derived-chain.

{{simple-single-agent}} (A1-7, tool access across domains) and {{peer-collaborative}}
(B3-1 through B3-4, multi-hop delegation) are concentrated
instances of this modifier. The substrate options for globally
meaningful identity (verifiable identifier formats, federated
identity providers, publicly anchored certificate chains) are a
binding question rather than a dimensional value.

### Recognisable Patterns {#recognizable-patterns}

For orientation, several combinations of dimensional values
correspond to patterns that readers may already recognise. The
following are illustrative, not exhaustive:

* Simple tool call: D1 caller-only, D2 atomic, D4 local-policy,
  D5 pre-bound. Recognisable as a REST API call or a simple
  tool invocation.

* Tool-using agent: D1 callee-may-initiate, D2 streamed, D4
  derived-1hop, D5 pre-bound. Recognisable as an LLM with
  function calling or an agent invoking external tools.

* Approval-gated job: D1 callee-may-initiate, D2 streamed, D3
  suspend-resume, D4 local-policy, D5 pre-bound. Recognisable
  as a continuous-integration pipeline awaiting manual approval.

{{peer-collaborative}}'s protocol requirements correspond closely to the
microservice-chain pattern applied to agents. {{authz-checkpoint}}'s
correspond closely to the approval-gated job pattern.

### Dimensions Considered but Not Adopted {#dimensions-not-adopted}

During the construction of this model, additional candidate
dimensions were considered and set aside. They are recorded here
so that the boundaries of the adopted model are explicit and so
that future revisions or companion documents can revisit them if
new use cases motivate doing so. For each candidate, the
dimension's intended purpose, candidate values, and the reason
it did not enter the adopted set are noted briefly.

* Communication cardinality. Intended to capture the number of
  endpoints addressed in a single exchange. Candidate values:
  one-to-one, one-to-many, many-to-many. In current agentic-
  protocol practice, one-to-many is realised as repeated one-
  to-one exchanges and does not introduce protocol-distinct
  primitives. Many-to-many would require group identity,
  dynamic membership, and replicated delivery semantics; no use
  case in {{usecases}} unambiguously requires this today. A use
  case requiring direct peer-mesh communication without a
  coordinating party would reactivate the dimension.

* Delegation transitivity. Intended to capture whether a
  receiving agent may forward work and to what depth. Candidate
  values: none, single-hop, transitive. The protocol-visible
  distinction between these values lies in the structure of the
  credential carried by the receiving agent, which is already
  captured by Authorization Derivation (D4) through its
  derived-1hop and derived-chain values. A separate dimension
  would duplicate that delta without adding new information.

* Mediation role. Intended to capture whether an interposed
  entity is present and what protocol-relevant function it
  performs. Candidate values: none, forwarding, translating,
  validating. The distinguishing behaviours of a mediator
  (cross-protocol translation, policy-based rejection) describe
  what the mediator does on its two interfaces taken together
  rather than a property of a single exchange. The
  protocol-layer requirements of {{tool-mediation}} are captured by
  extensions (EXT-AUDIT, EXT-XLATE) on the baseline.

* Modality profile. Intended to capture the categories of data
  format carried and whether the active set is negotiated.
  Candidate values: single, negotiated-multi. Only the
  negotiated-multi value is exercised by {{usecases}}, and the
  negotiation primitive itself is captured as an extension
  (EXT-MODNEG). A future use case requiring real-time
  multimodal interaction (for example, voice or video among
  agents) would likely motivate a transport-quality-of-service
  dimension rather than reactivating modality as defined here.

* Failure and delivery semantics. Intended to capture whether
  the agent protocol exposes a delivery guarantee. Candidate
  values: at-most-once, at-least-once, idempotent-keyed. In
  current practice the agent layer is uniformly at-most-once
  and stronger guarantees, where needed, are provided by the
  substrate to which the agent protocol is bound. A use case
  requiring exactly-once-effect (for example, transactional or
  financial agent operations) would reactivate this dimension.

* Trust domain span. Intended to capture whether an exchange
  is contained within one administrative domain or crosses
  administrative boundaries. Candidate values: intra-domain,
  cross-domain. Cross-domain operation does not introduce new
  exchange-level primitives; it tightens the practical value
  sets on Authorization Derivation (D4) and Endpoint Binding
  (D5). It is therefore captured by the deployment-context
  modifier of {{cross-domain-modifier}} rather than as an independent
  dimension.

### Open Questions for Further Work {#open-questions}

The dimensional model surfaces a small number of design choices
that the present document does not resolve and that may be useful
to address in a future revision or in companion documents:

* The mediator pattern of {{tool-mediation}} admits two distinct
  behaviours on D4: preserving the originator's authorization
  chain to downstream systems, or terminating the chain at the
  mediator. Each has different audit and trust implications.

* {{cooperative-reasoning}}'s communication topology determines whether D3 is
  durable-reattach (with a persistent task identifier) or none
  (with application-layer iteration over independent exchanges).
  The protocol implications differ materially.

* Several extensions identified in {{extensions}} may be candidates
  for cross-use-case standardisation rather than per-use-case
  treatment. The boundary between baseline and extension is a
  matter for community discussion.

* The substrate facets enumerated in {{substrate}} each admit
  multiple existing binding candidates. Specifying which
  bindings are profiled by this work, and which are left to
  implementation, will shape the scope of subsequent
  standardisation.

* Several candidate dimensions enumerated in {{dimensions-not-adopted}} are
  conditional on use cases that the present document does not
  describe. Operators or implementers with deployments that
  exercise those candidates are encouraged to surface them so
  that the model and the use case set can evolve together.
  
# Security Considerations {#security}

Security considerations are addressed throughout this document via
the Identity, Authentication, and Delegation requirements defined
for each use case. Agent identity and authentication mechanisms are
further discussed in [KLRC].

Agent identity information is considered to be sensitive, particularly
in multi-domain deployments. Use of persistent identifiers across
sessions and domains can enable tracking and correlation of agent
activity. Protocol designers need to consider mechanisms such as
pseudonymous or temporary identifiers to reduce linkability,
while preserving the ability to audit and enforce accountability
where required. The trade-offs between privacy, accountability,
and traceability need to be considered in the design of agent identity mechanisms.

# IANA Considerations {#iana}

This document has no IANA actions.

# Acknowledgements
{:numbered="false"}

Thanks to Borislava Gajic, Julien Maisonneuve, Parisa Foroughi, Laurent Ciavaglia, Peter Leis and Sina Khatibi for the discussions and comments.
