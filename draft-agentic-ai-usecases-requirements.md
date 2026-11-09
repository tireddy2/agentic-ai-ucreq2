---
title: "Agentic AI Use Cases and Requirements"
abbrev: "agentic-ai-ucreq"
docname: draft-agentic-ai-usecases-requirements-latest
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

  COSCIENTIST:
    title: "Accelerating scientific discovery with Co-Scientist"
    author:
      - org: Google DeepMind
    target: https://arxiv.org/abs/2502.18864

  TR23801-01:
    title: "Study on Architecture for 6G System"
    author:
      - org: 3GPP
    target: https://www.3gpp.org/ftp/Specs/archive/23_series/23.801-01/
    seriesinfo:
      3GPP: TR 23.801-01 v0.9.0

  YAO:
    title: "Problem Space Analysis of AI Agent Protocols in IETF"
    target: https://datatracker.ietf.org/doc/draft-yao-catalist-problem-space-analysis

  SONG:
    title: "Problem Statement and Requirements for Dynamic Multi-agent Secured Collaboration"
    target: https://datatracker.ietf.org/doc/draft-song-dmsc-problem-statement

  RFC7696:
    title: "Guidelines for Cryptographic Algorithm Agility and Selecting Mandatory-to-Implement Algorithms"
    target: https://www.rfc-editor.org/rfc/rfc7696

  AUTOGEN:
    title: "AutoGen: A Framework for Multi-Agent Conversation"
    target: https://microsoft.github.io/autogen/stable/

  LANGCHAIN:
    title: "LangChain Agent Framework"
    target: https://python.langchain.com/docs/concepts/agents/

  OPENAI-AGENTS:
    title: "OpenAI Agents SDK"
    target: https://openai.github.io/openai-agents-python/

  LIVE-AGENT:
    title: "Live Agent Use Case Extensions for Agentic Dialogs"
    target: https://www.ietf.org/archive/id/draft-agentproto-live-agent-use-case-00.html
    author:
      - ins: Y. Liu
        name: Yanmei Liu
      - ins: D. Liu
        name: Dapeng Liu
    date: 2026-10-29
    seriesinfo:
      Internet-Draft: draft-agentproto-live-agent-use-case-00
    refcontent: Work in Progress

--- abstract

This document describes use cases for agentic AI communication systems
and derives protocol requirements from those use cases. The requirements
are intended to guide IETF standardization work on protocols in the
context of agent-to-agent communication, agent-to-tool communication,
with focus on dialog management, transport, multimodal communication,
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
drive development of the agentic dialog management protocol, its
transport bindings, and the reference architecture for agentic AI systems.

Each use case contributes a distinct slice of requirements, and it is their
composition that distinguishes an agentic communication protocol from ordinary
application-to-service invocation.

The use cases in this document cover interaction patterns for
agentic AI communication systems. This document takes into account
related use case and problem statement documents including [SCRM],
[YAO], and [SONG], and existing protocol work including
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
agent communication, such as text, audio, image, or video. A dialog
may support one or more modalities simultaneously.

**Orchestrator Agent**: An agent that acts as a controller,
coordinating the activity of other agents by decomposing goals into
sub-tasks and delegating those sub-tasks to appropriate agents.

**Peer Agent**: An agent that receives delegated subtasks from another
agent and may itself delegate further to other agents.

**Dialog**: The correlated sequence of interactions between two or
more participants (users, agents, and tools) over the course of a task. A
dialog may persist across multiple individual message exchanges and network
connections.

**Dialog Context**: The identifiers and lifecycle state needed to
correlate and maintain a dialog. It does not include the content exchanged
with AI models, such as conversation memory, retrieved documents, or prompts.

**Linked Dialog**: A new dialog, with a new dialog context, that a
participant creates to continue a dialog it received.

**Task Context**: The data that a participant maintains
to execute a task, such as conversation history and intermediate
results. Task context is not carried by the protocol.

**Task**: A unit of work submitted by a user to an agent, or
delegated by one agent to another.

**Task State**: The current execution status of a task (e.g., pending,
in-progress, completed, failed).

**Tool**: An external service invoked by an agent to retrieve data or
perform operations. A tool is not necessarily an agent and may not participate
in agent-to-agent communication.

**User**: A human that initiates interaction with an
AI agent by submitting a request or task.

## Dialog Context and Task Context {#dialog-task-context}

Any participant, including an intermediary, can use
dialog context to correlate and maintain a dialog without interpreting
the task. For example, if the transport connection between two agents
is interrupted, the dialog identifier allows the reconnecting agent to
re-associate with the existing dialog instead of starting a new dialog
and restarting the task. An intermediary uses the dialog identifier to
correlate the requests it receives with the requests it forwards.
Neither function requires access to the task context. Each participant
can use the dialog identifier to retrieve its own task context.

# Common Protocol Requirements {#common-requirements}

The following baseline requirements apply to both agent-to-agent and agent-to-tool protocol interactions across all use cases and are not repeated per use case.

Each per-use-case requirement is tagged with one or more of the following protocol area tags to allow cross-use-case navigation:

- **Dialog**: Requirements related to dialog correlation, lifecycle, continuity, and recovery.
- **Transport**: Requirements related to message delivery, streaming, cancellation, and data transfer.
- **Security**: Requirements related to confidentiality, integrity, and authorization.
- **Authentication**: Requirements related to identity verification and credential delegation.

| REQ-ID | Description | Tag |
|--------|-------------|-----|
| CMN-1  | The protocol is required to support any client application to communicate with any agent service. | Authentication |
| CMN-2  | Mutual authentication is required between all communicating parties. | Authentication |
| CMN-3  | All protocol traffic is required to be encrypted and integrity-protected in transit. | Security |
| CMN-4  | Structured error responses are required to include an authorization scope violation type, reported by the orchestrator or mediator when an agent attempts an action that exceeds or contradicts the scope delegated to it. | Security |
| CMN-5  | Structured error responses are required, distinguishing at minimum: authentication failure, authorization failure, timeout, and internal error. | Transport |
| CMN-6  | The protocol is required to provide a means to signal task priority so that critical-path tasks can be scheduled ahead of lower-priority ones. | Transport |
| CMN-7  | The protocol is required to support cryptographic algorithm agility, ensuring that cryptographic algorithms used for encryption, authentication, credential verification, and integrity protection can be negotiated and updated over time, in accordance with {{RFC7696}}. | Security, Authentication |
| CMN-8  | The protocol is required to provide a means to verify the agent authentication credentials validity used by agents at the time of use. | Authentication |
| CMN-9 | The protocol is required to support signaling that a presented credential has been revoked or is otherwise invalid. | Security |
| CMN-10 | The protocol is required to support revoking a previously granted authorization, including propagating the revocation across a delegation chain so that affected agents cease to act under it. | Security |
| CMN-11 | The protocol is required to support establishment, modification, and termination of a dialog, distinct from cancellation of a delegated subtask within the dialog. Modification includes changes to the modalities or lifetime of the dialog. The protocol is required to support a dialog lifetime, after which the dialog is terminated. | Dialog |
| CMN-12 | Before accepting a change to a dialog, a participant is required to be able to verify that the change is authorized at that time. If this check fails or cannot be completed, the participant is required to reject the change and signal the reason. It is required to be able to determine which of the two cases occurred. | Dialog, Security |

## Network-Layer Assumptions {#network-assumptions}

The requirements in this document assume that messages
that establish, modify, resume, or terminate a dialog are delivered
reliably and in order. Data for real-time modalities, such as
interactive audio and video, can be delivered without reliability or
ordering to meet latency requirements. All the deliveries are
congestion controlled. Whether these share a transport connection is
determined by the transport bindings.

# Use Cases {#usecases}

These interactions differ from ordinary application-to-service invocation in
several respects, one being runtime selection of agents and tools by capability.
An agent's internal processing, including perception, planning and
re-planning, and invocation of its AI model, is not visible on the protocol
interface and is out of scope; it motivates the use cases but drives no
protocol requirement. Cross-domain operation can apply to any of
these use cases. Crossing an administrative boundary adds authentication and
authorization requirements, which map to the OAuth and WIMSE work discussed in
{{relationship-oauth-wimse}}, and privacy requirements on dialog context
propagated across the boundary, discussed in {{security}}. The use
cases are independent of deployment model: they apply whether participants are
within a single administrative domain or span several, and whether they
interact directly or through intermediaries. Together they cover the
deployment models in scope: a client application on a user's device and
an agent ({{simple-single-agent}}), agents hosted by different
organizations or service providers ({{peer-collaborative}}), and agents
reached through a mediator such as a network operator's agent gateway
({{tool-mediation}}).

## Simple Single-Agent Task {#simple-single-agent}

### Description

A user submits a task to an AI agent via a client application. The
client application initiates a dialog with the agent, which creates and
maintains it. The agent executes the task by invoking one or more tools and returns
results to the user. The tools invoked by the agent may reside in
the same or a different administrative domain. The agent protocol
is required to support multiple input and output modalities, and
the client application and agent are required to be able to negotiate which
modalities are active for the dialog.

This use case covers the protocol interface between the client
application and the agent. The interaction between the user and
the client application is out of scope in this use case. This use case assumes that
the user communicates with the agent via a client application;
direct communication between a user and an agent without an
intermediary client application is not covered in this use case.

This interaction pattern is described in [SCRM].

The same pattern also applies to an ongoing live task, in which input
observations continue while responses and tool operations are in
progress. Sources, responses, and operations can change independently
within the continuing dialog ({{live-interaction}}).

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

### Examples

#### Travel Booking

A user books a flight through a travel booking app. The app is the
client application. It sends the user's request to a travel agent run
by the travel company that provides the app. The agent uses two
tools: a fare search tool run by the travel company, and a booking
tool run by an airline, in a different administrative domain.

~~~
+------------+      +--------------+      +----------------+
| Travel app |<---->| Travel agent |----->| Fare search    |
|            |      |              |      | (travel co.)   |
+------------+      +--------------+      +----------------+
                           |
                           |              +----------------+
                           +------------->| Flight booking |
                                          | (airline)      |
                                          +----------------+
~~~

1. The app establishes a dialog with the agent and asks for a flight on
   a given route and date (CMN-11).

2. The agent queries the fare search tool and returns matching flights
   to the app.

3. The user picks a flight. The agent calls the airline's booking
   tool with a credential showing that the user authorized the
   booking, without the user's airline credentials (A1-9).

4. The agent returns the booking confirmation, and the app terminates
   the dialog (CMN-11).

If the user abandons a search before step 3, the app cancels that task
(A1-2). The dialog continues, and the user can start a new search in
it without starting over.

#### Live Interaction with Changing Sources and Devices {#live-interaction}

A user asks an agent to explain an object shown by a camera through a
client application. The client sends observations while the agent returns
speech, text, or visual annotations, and tools may continue working
while the user provides further instructions. Observation sources,
responses, and delegated operations can change independently within
the continuing dialog. The client, agent, and relevant speech or media
intermediaries can be independently implemented.

This extension retains the client application, agent, tool, and
mediator roles of this use case. It concerns one user's interaction
with an agent through a client, including the agent's tool access.
A device or sensor does not by itself introduce a new participant.
The scenarios are developed in [LIVE-AGENT].

1. The client establishes dialog D and agrees on active modalities.
   The application associates its sources and outputs with D.

2. The agent begins response R1 associated with observation O1 while
   further input continues to arrive.

3. The user interrupts R1 and points the camera at another object.
   The client stops local presentation and requests the intended
   remote effect on R1. Other work in D can continue.

4. The camera stops providing observations while audio continues.
   The application distinguishes a change in source availability from
   a modification of the dialog's agreed modalities.

5. Output for R1 or O1 arrives after the change. Its association and
   application validity remain distinguishable from current output.
   This can occur even if R1's generation has already completed.

6. If communication is interrupted, dialog resumption and application
   state reconciliation are handled separately. Unknown source or
   operation state remains explicit until it can be established.

**Device and operation variants.**

In a glasses-and-phone deployment, the phone can host the client and
represent the glasses' sources and outputs. Removing the glasses or
stopping their camera need not disconnect the phone. The same user can
continue on the phone, changing the active source and authorized output
recipient. Delayed data or reconnection of the glasses does not by
itself restore their former role or permissions. If devices host
separate clients, the application distinguishes an authorized
participant change from a source change within one client. A change
of source or output represented by the phone client is distinct from
the client itself continuing on another device under A1-11.
Battery or connectivity constraints can reduce source availability
without requiring detailed device telemetry on the protocol interface.

In an embodied deployment, the agent converses with the user while a
controller performs an authorized operation exposed through a tool or
mediator ({{tool-mediation}}). Stopping an explanation does not establish
that the operation stopped. A request to change the operation has its
own authorization and outcome. If connectivity fails before that
outcome arrives, recovery combines the uncertain-operation handling in
{{orchestrator-agent}} with current source and observation state.
Physical control, local safety, and emergency-stop mechanisms remain
with the device and domain specifications.

### Protocol Requirements {#a1-protocol-requirements}

This use case involves two distinct protocol interfaces. The App/Agent-to-Agent
interface is between the party making the request, which may be a client
application or another agent, and the agent that handles it. The Agent-to-Tool
interface is between the agent and the tools it invokes. A given requirement
does not necessarily apply to both; the Interaction column indicates which
interface each applies to.

| REQ-ID | Description | Interaction | Tag |
|--------|-------------|-------------|-----|
| A1-1  | The protocol is required to support incremental streaming of agent output, allowing partial results to be delivered to the client before the agent has completed processing. | App/Agent-to-Agent | Transport |
| A1-2  | The protocol is required to define a task cancellation message that the client can issue at any point during task execution. | App/Agent-to-Agent | Transport |
| A1-3  | The protocol is required to define structured error message types that distinguish at minimum: transport failure, tool invocation failure, and agent processing failure. | Both | Transport |
| A1-4  | The protocol is required to support multiple modalities for both input and output, including real-time audio and video with low latency. | App/Agent-to-Agent | Transport |
| A1-5  | The protocol is required to support modality negotiation at dialog establishment, allowing the client and agent to agree on which modalities are active for the dialog. | App/Agent-to-Agent | Dialog, Transport |
| A1-6  | The protocol is required to support agent-initiated notifications to the client during task execution. | App/Agent-to-Agent | Transport |
| A1-7  | The protocol is required to support concurrent invocation of multiple tools within a single agent task, where tools may be operated by distinct providers across different administrative domains, each with independent authentication and authorization requirements. | Agent-to-Tool | Transport, Security |
| A1-8  | The protocol is required to support bulk transfer of large data between communicating parties, applicable to both agent-to-tool and agent-to-agent interactions. | Both | Transport |
| A1-9 | A delegation mechanism is required to be defined by which an agent presents to a tool provider a credential attesting the authorization for the requested tool access, without exposing the client's primary credentials. | Agent-to-Tool | Authentication |
| A1-10 | The protocol is required to preserve the dialog context when a participant transforms data from one modality to another, such as audio to text. | App/Agent-to-Agent | Dialog |
| A1-11 | The protocol is required to allow the user to continue a dialog from the same client application on another device. | App/Agent-to-Agent | Dialog |

#### Live Interaction Refinements {#live-requirements}

A1-1, A1-4, and A1-5 provide the streaming and modality basis. CMN-11
and CMN-12 provide dialog lifecycle and authorized modification;
A1-11 covers continuation by the same client application on another
device. A1-2, B1-5, and B3-10 provide task cancellation and its
propagation. B1-6 through B1-8 provide dialog resumption and its checks;
{{orchestrator-agent}} describes the handling of uncertain operation
outcomes. These capabilities are inherited rather than repeated as new
requirements for real-time media.

The refinements below concern the application protocol or profile used
for live interaction. A binding and its application integration
associate dialog context with application signaling and data flows,
using the existing correlation requirements, including A1-10, B3-9,
and B5-3 where applicable. The Responsibility column identifies which
layer supplies the required semantics. Source, response, observation,
and model state remain outside the common dialog context. Existing
protocol mechanisms are to be reused where they provide the required
semantics; changes to those protocols remain with their respective
maintainers.

| REQ-ID | Description | Responsibility | Tag |
|--------|-------------|----------------|-----|
| LIVE-1 | Where delayed data could be confused with current input or output, the application protocol or profile is required to distinguish the applicable source instance or exchange and associate relevant output with the observations or observation range to which it refers. The necessary associations are required to remain determinable through relevant transformations, source replacements, and restarts. | Application/profile; binding for dialog association | Dialog, Transport |
| LIVE-2 | For supported response controls, the application protocol or profile is required to identify the target and intended effect, distinguishing control of response generation or output delivery from task cancellation and dialog termination. It is required to distinguish acceptance from effect, expose failed or unresolved outcomes, and define the treatment of completion races and late or superseded output. | Application/profile; existing task cancellation where applicable | Transport |
| LIVE-3 | The application protocol or profile is required to distinguish source capability, current availability, and permission, and make unavailable or unknown observation state explicit. Signaled changes are required to have a determinable scope, outcome, and applicability to subsequent exchanges, including fallback or failure when no acceptable mode remains. The profile is required to define when last-known observations cease to be treated as current and how repeated or delayed updates are handled. Missing media alone does not establish the cause of unavailability. Continued dialog or connection activity does not by itself make an earlier observation current. A capture pause need not modify the dialog's agreed modalities. | Application/profile; existing dialog modification and authorization where applicable | Dialog, Transport, Security |
| LIVE-4 | The application protocol or profile is required to distinguish connection state, participant changes, and changes among devices or sources represented by one client. It is required to establish the applicable input sources and authorized output recipients after a change; a continuing connection, delayed data, or device reconnection does not by itself reinstate an earlier role or permission. | Application/profile; existing dialog lifecycle and authorization | Dialog, Security |
| LIVE-5 | After dialog resumption, the application protocol or profile is required to reconcile applicable sources, modalities, output recipients, response-control associations, and unfinished operations. Unresolved state and operation outcomes are required to remain explicit; uncertain operations are handled as described in {{orchestrator-agent}}. Resumption alone does not authorize an operation retry, source reactivation, or reuse of retired control associations or permissions. | Application/profile; existing dialog resumption and authorization | Dialog, Transport, Security |

A response represented as a task uses the existing cancellation and
outcome-reporting mechanisms. LIVE-2 specifies the scope and effects of
response controls supported by the application; it does not require a
separate task cancellation mechanism or prescribe local playback behavior.

The profile defines its supported controls and the interpretation of
time references and discontinuities. An input association can cover
multiple observations; it does not prove which data an AI model
actually used. Source identifiers need not be globally persistent.
The application can retain mappings when a transformation changes
identifiers, rather than requiring every hop to use the same value.

Control and media can fail independently, so their applicable
associations and application recovery rules need to remain defined
when one path is unavailable. Reliable dialog-control delivery follows
{{network-assumptions}}; it does not establish a global ordering of
all media and signaling. Latency budgets, transport selection, and
binding mechanisms remain subsequent protocol work.

## Orchestrator and Agent Collaboration {#orchestrator-agent}

### Description

An orchestrator agent acts as a controller, decomposing a task into
subtasks and delegating them asynchronously to one or more other agents.
The orchestrator decides which other agents to invoke, sequences the
delegation, and aggregates results to continue task execution. Each
agent executes the respective subtask independently and reports results back
to the orchestrator.

It should be noted that AI models are stateless by nature: each inference
call processes only what is explicitly provided with a particular context,
with no persistent memory between calls. The application
layer is responsible for maintaining the context across the calls by
carrying conversation history, intermediate results, and task
state. Dialog continuity is therefore required to preserve the
dialog context across network interruptions, so that a reconnecting
agent can re-associate with the dialog and use it to restore its own
task context without reconstructing it from scratch.

If a dialog is interrupted before the result of an operation reaches
the delegating agent, the operation may or may not have completed.
Resuming the dialog does not tell the delegating agent which. Before
resending the operation, the delegating agent checks its outcome with
the delegated agent or tool that performed it. If the outcome cannot be
determined, the operation remains unresolved, and resuming the dialog
alone does not justify resending it.

This pattern is reflected in [A2A],
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

### Example

A user asks a trip planning agent, the orchestrator, to arrange a
business trip. The orchestrator delegates the flight booking to a
flight agent and the hotel booking to a hotel agent. Both agents are
run by different providers.

~~~
+--------------+      +--------------+
|              |----->| Flight agent |
| Trip planner |      +--------------+
| (orchestr.)  |
|              |      +--------------+
|              |----->| Hotel agent  |
+--------------+      +--------------+
~~~

1. The orchestrator establishes a dialog with each agent and delegates
   both subtasks without waiting for either to finish (B1-1, B1-2).

2. The flight agent streams fare options and reports the booked
   flight (B1-4, B1-3).

3. The orchestrator sends the hotel agent a request to book a room.
   The hotel agent books the room, but the connection drops before its
   confirmation reaches the orchestrator.

4. The orchestrator reconnects and resumes the dialog with the hotel
   agent (B1-6). The hotel agent verifies that the orchestrator is
   the participant whose exchange was interrupted, and that the dialog
   is current and still authorized (B1-7).

5. Instead of sending the booking request again, which could book a
   second room, the orchestrator asks the hotel agent for the outcome
   of its request. It learns that the room was booked and uses that
   booking.

### Protocol Requirements {#b1-protocol-requirements}

| REQ-ID | Description | Tag |
|--------|-------------|-----|
| B1-1  | The protocol is required to facilitate task delegation for an orchestrator to agents that includes task delegation and acknowledgement message types. | Transport |
| B1-2  | The protocol is required to support asynchronous delegation, allowing the orchestrator to delegate to multiple agents without waiting for each to complete before proceeding. | Transport |
| B1-3  | The protocol is required to define a result reporting message by which an agent returns its completed output to the orchestrator. | Transport |
| B1-4  | The protocol is required to support streaming of intermediate results from the agent to the orchestrator during task execution. | Transport |
| B1-5  | The protocol is required to define a task cancellation message that the orchestrator can send to an agent to abort a delegated subtask. | Transport |
| B1-6  | The protocol is required to support persistent dialog identifiers that survive network interruption or change, and is required to define a dialog resumption message by which an agent re-attaches to an interrupted dialog, restoring the prior dialog context. | Dialog |
| B1-7  | On resumption, the protocol is required to enable the receiving participant to verify that (1) the party resuming the dialog is the participant whose exchange was interrupted, (2) the dialog context presented is the current state of that dialog, and (3) the dialog has not been terminated and its authorization has not been revoked. | Dialog, Security |
| B1-8  | If any check in B1-7 fails or cannot be completed, the dialog is not resumed and the protocol is required to signal the reason. The receiving participant is required to be able to determine whether the check failed or could not be completed. | Dialog, Security |

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

### Example

A user asks a banking assistant, the orchestrator, to close an
account. The orchestrator delegates the closure to an account closure
agent, which runs a workflow that can take several days.

~~~
+------------+      +--------------+      +---------------+
|    User    |<---->|   Banking    |<---->| Account       |
|            |      |  assistant   |      | closure agent |
+------------+      +--------------+      +---------------+
~~~

1. The orchestrator establishes a dialog with the closure agent and
   delegates the closure. The dialog lasts for the whole workflow.

2. The closure agent cancels the account's standing payments and
   closes the linked debit card. After each step it sends the
   orchestrator a progress notification, such as "standing payments
   cancelled" (B2-1).

3. Before transferring the remaining balance, the closure agent
   pauses and sends an authorization checkpoint request stating the
   amount and the destination account (B2-2, B2-6).

4. The orchestrator relays the request to the user, who approves it
   (B2-3). The approval covers only this transfer (B2-5).

5. The closure agent completes the transfer and reports the result.
   If no response had arrived before the timeout, it would have
   halted the transfer and treated the request as unresolved (B2-4).

### Additional Protocol Requirements {#b2-protocol-requirements}

This use case builds on the requirements defined for {{orchestrator-agent}}
and introduces additional requirements specific to authorization checkpoints in delegated tasks.

| REQ-ID | Description | Tag |
|--------|-------------|-----|
| B2-1  | The protocol is required to support agent-initiated progress notifications to the delegating agent during task execution. | Transport |
| B2-2 | The protocol is required to define an authorization checkpoint request, by which an agent pauses task execution and asks the orchestrator to authorize a specific action before proceeding. The request is required to describe the action to be performed and the effect it would have, so the orchestrator has enough information to decide. | Transport, Security |
| B2-3 | The protocol is required to define the responses to an authorization checkpoint request, which include at minimum approve, deny, and approve with modified parameters. A denial is required to be conveyed as an explicit error response. |  Transport, Security |
| B2-4  | The protocol is required to support a response timeout, after which the agent treats the request as unresolved and halts the affected subtask. |  Transport |
| B2-5 | An authorization checkpoint response is required to authorize only the action in the request it answers, including any approved modification to that action, and not any different or later action. | Transport, Security |
| B2-6 | The protocol is required to support authorization requested and granted at the granularity of a specific operation, so that a sensitive action is authorized individually rather than covered by a broad, pre-existing authorization. | Transport, Security |

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

This use case is described in [A2A].

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

### Example

A hiring manager asks a company's hiring agent to find candidates for
a job listing. The hiring agent delegates sourcing to a recruitment
agency's sourcing agent. The sourcing agent searches a job board tool
and delegates screening of the results to a screening agent, which
assesses each candidate against the listing. The hiring agent later
delegates interviews to a scheduling agent. Each agent is run by a
different provider, in its own administrative domain.

~~~
+--------+    +----------+    +-----------+
| Hiring |--->| Sourcing |--->| Screening |
| agent  |    |  agent   |    |   agent   |
+--------+    +----------+    +-----------+
    |              |
    v              v
+------------+  +-----------+
| Scheduling |  | Job board |
|   agent    |  |   tool    |
+------------+  +-----------+
~~~

1. The hiring agent establishes a dialog with the sourcing agent and
   delegates the search, with an authorization limited to this job
   listing (B3-1).

2. The sourcing agent searches the job board tool, then delegates
   screening to the screening agent. Because the screening agent is in
   another domain, the sourcing agent creates a new dialog linked to
   the one it received (B3-9). It passes an authorization narrowed to
   reading the candidate profiles for this listing (B3-1, B3-8). The
   screening agent can determine that the hiring manager originally
   authorized the task (B3-2).

3. If the screening agent becomes unavailable, the sourcing agent
   detects this and halts that subtask (B3-5).

4. The hiring manager picks candidates, and the hiring agent delegates
   interview scheduling to the scheduling agent, in a new dialog
   linked to the first.

### Additional Protocol Requirements {#b3-protocol-requirements}

This use case builds on the requirements defined for {{orchestrator-agent}}
and introduces additional requirements specific to multi-hop delegation chains.

| REQ-ID | Description | Tag |
|--------|-------------|-----|
| B3-1  | The protocol is required to support multi-hop delegation chains, where an agent that receives a delegated subtask may itself delegate further to other agents. At each hop, the delegating agent is required to present a credential that does not exceed the authorization scope of the credential it received. | Authentication, Security |
| B3-2  | The protocol is required to preserve the identity of the originating entity across all hops in the delegation chain, such that any agent in the chain can determine the identity of the entity that originally authorized the task. | Authentication, Security |
| B3-3  | The protocol is required to support transferable credentials that carry the original authorization constraints across all hops in the delegation chain. Each receiving agent is required to be able to cryptographically verify that the credential presented to it was issued by the delegating agent and that the chain of delegation traces back to the original authorization. | Authentication, Security |
| B3-4  | The protocol is required to ensure that authorization granted to an agent in a delegation chain, including for tool invocations, is derived from the authorization issued by the initiating agent, and not from the identity or authorization scope of any intermediate agent in the chain. | Authentication, Security |
| B3-5 | The protocol is required to allow a delegating agent to detect that a delegated agent has become unavailable and to halt the affected in-flight subtask. | Dialog, Transport |
| B3-6 | The protocol is required to provide a verifiable provenance record of a request and its response as they traverse the delegation chain, attributing each transformation to the hop that performed it, such that removal of a hop, or a modification not attributable to a signing hop, is detectable. | Authentication, Security |
| B3-7 | The protocol is required to enable a receiving party to detect and reject a delegation chain that contains a cycle, such as a chain in which the receiving party's own identifier already appears. | Authentication, Security |
| B3-8 | The protocol is required to enable a receiving party to determine that the authority conveyed to it does not exceed the authority conveyed to the hop it received from. | Authentication, Security |
| B3-9 | The protocol is required to propagate the dialog context across each hop of a delegation chain, so that participants can correlate interactions along the chain. A participant may instead create a new dialog, with a new dialog context, linked to the one it received. | Dialog |
| B3-10 | The protocol is required to propagate cancellation of a delegated subtask, and termination of a dialog, along the delegation chain, and to report to the party that initiated it whether the cancellation or termination took effect further down the chain, or that this could not be determined. | Dialog |

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

### Example

A research team uses a multi-agent research assistant [COSCIENTIST] to
propose hypotheses for a research goal. A supervisor agent, acting as
the coordinator, distributes the goal to generation agents that
propose hypotheses, a reflection agent that reviews them, and a
ranking agent that ranks them.

~~~
                 +------------------+
                 | Supervisor agent |
                 +------------------+
                  /       |        \
                 v        v         v
        +------------+ +------------+ +---------+
        | Generation | | Reflection | | Ranking |
        |   agents   | |   agent    | |  agent  |
        +------------+ +------------+ +---------+
~~~

1. The supervisor establishes a separate dialog with each agent and
   sends it the research goal.

2. The generation agents propose hypotheses independently.

3. The reflection agent reviews each hypothesis, and the ranking agent
   ranks them. The supervisor shares the results with all agents for
   the next round.

4. After several rounds, the supervisor returns the top-ranked
   hypotheses to the research team and terminates the dialog
   (CMN-11).

## Tool, Data, and API Mediation Between Agents {#tool-mediation}

### Description

In many multi-agent deployments, access to external resources such as
APIs, databases, enterprise systems, or hardware interfaces is
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

The mediator can be deployed as a proxy between an agent and its
tools, such as an MCP proxy. The 3GPP 6G architecture study
[TR23801-01] describes it as an agent gateway through which
third-party agents use network agents.

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

### Example

A mobile network operator runs an agent gateway through which
authorized third-party agents use network agents. A concert organizer's agent asks for extra network
capacity at the venue during the concert.

~~~
+-------------+      +----------+      +--------------------+
| Organizer's |----->|  Agent   |----->| Network agent      |
| agent       |      | gateway  |      |                    |
+-------------+      +----------+      +--------------------+
~~~

1. The organizer's agent establishes a dialog with the gateway and
   requests extra capacity at the venue for the hours of the concert.

2. The gateway checks that the request matches the organizer's
   authorization and forwards it to the network agent.

3. The gateway confirms the added capacity to the organizer's agent.

4. After the concert, the organizer's agent terminates the dialog
   (CMN-11).

### Additional Protocol Requirements {#b5-protocol-requirements}

| REQ-ID | Description | Tag |
|--------|-------------|-----|
| B5-1  | The protocol is required to define error response types for request validation failure (rejected due to potential unintended or irreversible side effects) and protocol translation failure (rejected on unsuccessful translation of a request or response between supported protocols), distinct from authorization failure. | Transport, Security |
| B5-2  | The protocol is required to support continuation of a dialog through a different mediator when the mediator in use fails. | Dialog |
| B5-3  | The protocol is required to enable a mediator that translates between agent communication protocols to carry the dialog context across the translation, so that participants on each side can correlate the dialog. | Dialog |

# Relationship to OAuth and WIMSE Work {#relationship-oauth-wimse}

Several requirements in this document concern authorization, delegation, and agent identity (CMN-4, CMN-8, CMN-9, CMN-10, B2-2 to B2-6, B3-1 to B3-4, B3-6, B3-7, B3-8). These are expected to be addressed in the OAuth and WIMSE Working Groups.

Rejecting a dialog change or a resumption when a check fails or cannot be completed (CMN-12, B1-7, B1-8) is a requirement for the dialog management protocol. The check itself, verifying identity and authorization, is expected to be addressed in the OAuth and WIMSE Working Groups.

Authorizing an individual action within a task, including when the authorization information is unavailable or may no longer be current, is also expected to be addressed in the OAuth Working Group.


# Existing Protocols and Gaps {#gaps}

[A2A] groups related tasks under a context identifier, and [MCP]
groups the requests between one client and one server under a session.
Both are scoped to a single hop. An agent that delegates to another
agent or calls a tool starts a new context or session, and no
identifier spans the hops. Cancellation is per task and per hop.

The gap is end-to-end: correlating a task across hops, protocols, and
connections, and propagating lifecycle actions such as cancellation
along the delegation chain. These are the dialog requirements in this
document (CMN-11, CMN-12, B1-6 to B1-8, B3-9, B3-10, B5-2, B5-3). They
can be met by carrying the dialog context across hops or by linked
dialogs.

This analysis reflects [A2A] and [MCP] at the time of writing.

# Accountability and Auditing {#accountability-auditing}

Accountability and auditing of agent actions are out of scope for this
document. Audit and provenance representation is addressed by existing work,
including W3C provenance (PROV) and Trace Context. Dialog identifiers can be
used to correlate audit records across the participants in a dialog.

Editor's note: Add a reference to IETF audit work once it progresses.

# Security Considerations {#security}

Security considerations are addressed throughout this document via
the Identity, Authentication, and Delegation requirements defined
for each use case.

Agent identity information is considered to be sensitive, particularly
in multi-domain deployments. Use of persistent identifiers across
dialogs and domains can enable tracking and correlation of agent
activity. Protocol designers need to consider mechanisms such as
pseudonymous or temporary identifiers to reduce linkability,
while preserving the ability to audit and enforce accountability
where required. The trade-offs between privacy, accountability,
and traceability need to be considered in the design of agent identity mechanisms.

Agent unavailability (B3-5) halts the affected subtask but does not imply
revocation, since it may be a transient failure rather than compromise.

Signaled error reasons help a participant decide on the right course
of action to address the error. They need to be precise enough for
that, but not reveal information that an attacker could use.

The confidentiality and integrity protection required by CMN-3 applies
to the signaling and data flows used in {{live-interaction}}.
Within that protected communication, applications need to use existing
authorization mechanisms to determine which parties may consume input
from a source, receive private output, or invoke a controller operation.
The applicable authorization scope and duration, and the effect of
revocation on active flows and operations, need to be defined. A dialog
or source identifier does not by itself confer authorization.

Forged associations can direct observations to the wrong dialog or
recipient. Replayed controls or delayed updates can affect a current
response or reinstate retired state. Application signaling and its
associations need authentication, integrity, and replay handling
appropriate to the trust boundaries, including intermediaries.
Untrusted media or model output does not acquire the authority of
authenticated control signaling merely by naming an operation.

Conversational participation does not establish physical-action
authority. Network delivery, an acknowledgment, local silence, or
disconnection does not establish that an action completed or stopped.
Its outcome is supplied by the relevant controller or domain protocol.
Implementations also need appropriate limits on continuous input,
generated output, retained recovery state, and repeated state changes
to avoid unbounded resource use. These considerations do not define a
new authorization protocol, audit format, or local safety mechanism.

# IANA Considerations {#iana}

This document has no IANA actions.

# Acknowledgements
{:numbered="false"}

Thanks to Borislava Gajic, Julien Maisonneuve, Parisa Foroughi, Laurent Ciavaglia, Nathalie Romo Moreno, Peter Leis, Linda Dunbar, Mikhail Sergeev, Iman Schrock, Sumit Ahuja, Sina Khatibi, Vaishnavi Gudur, Lars Kersten Kroehl, Girish Konda, Nikhil Donapati, Nicholas Templeman, Bradley B, Douglas Wadkins, and Lionel Morand for the discussions and comments.

The examples in {{simple-single-agent}} and {{authz-checkpoint}} are
based on scenarios in draft-rosenberg-agentproto-usecases by Jonathan
Rosenberg and Cullen Jennings.
