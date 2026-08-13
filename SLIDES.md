# Fantastic Agents and Where to Find Them

Working subtitle: **An illustrated field guide to running agents in production**

Target: 30-minute LangChain/LangGraph meetup talk, including a small amount of audience reaction time but excluding Q&amp;A.

This is a production brief, not final slide copy. Each slide records what it must accomplish, what appears on screen, what is said, and what still needs to be made.

## Scope and confidentiality

This is explicitly a talk about **agents in production**: authorization, failure recovery, observability, evaluation, persistence, and human oversight around real runtimes. It is not a case study of the speaker’s employer.

- All scenarios, architectures, names, policies, data, and failure stories are synthetic composites.
- Do not use employer-specific screenshots, metrics, incidents, terminology, vendors, schemas, or topology.
- Describe diagrams as “a representative production architecture,” never “our architecture.”
- Examples may feel operationally concrete without claiming that any named organization deployed them.
- If asked whether these are systems used at the speaker’s company: “These are general production patterns and fictional examples; I can’t discuss internal implementations.”

## The governing idea

> Agents become harder to build not because they become smarter, but because we give them more authority.

The talk uses Simon Willison’s deliberately narrow definition:

> “An LLM agent runs tools in a loop to achieve a goal.” — Simon Willison

Source: <https://simonw.substack.com/p/i-think-agent-may-finally-have-a>

This makes the production harness visible: the model requests a tool, application code executes it, and the result returns to the model until a bounded goal or stopping condition is reached. All three specimens in this talk satisfy the definition. What changes is the authority of their tools and how much of the surrounding process the model is allowed to control.

The architecture question that follows is:

> How much of the control flow are we willing to delegate to the model?

The talk moves through three levels of authority:

1. **KNOW** — freedom to synthesize an answer from evidence.
2. **DO** — freedom to select and invoke capabilities.
3. **DECIDE** — freedom to contribute to a consequential judgment inside a process.

These are authority archetypes, not exclusive categories. A DO agent also knows things; a DECIDE agent may also use tools. We classify each creature by the most consequential freedom it possesses.

## The invented bestiary

The names are intentionally mock-taxonomic rather than strict classical Latin. They should always appear in italics, like biological binomials.


| Common name     | Specimen                | Human analogy | Defining instinct                          | Implementation                       |
| --------------- | ----------------------- | ------------- | ------------------------------------------ | ------------------------------------ |
| **Sourcehound** | *Archivora citans*      | Librarian     | Follows evidence and leaves a source trail | Retrieval agent + read-only tools   |
| **Keypaw**      | *Instrumentrix cauta*   | Concierge     | Selects a capability and acts              | LangChain agent + tools + middleware |
| **Caseweaver**  | *Inquisitor probabilis* | Investigator  | Constructs and assesses a case             | Explicit LangGraph workflow          |


The common names should be used in most spoken narration; the binomials appear on specimen cards and section reveals. The human analogies explain their jobs but are not creature names. All names are provisional. Before visual production, check that they are pronounceable on stage and do not accidentally match existing fictional or commercial properties.

The common names encode the architecture rather than merely describing the artwork:

- **Sourcehound** follows a scent to evidence and should leave a trace back to it.
- **Keypaw** can open access to specialized capabilities, but every “key” needs an authorization boundary.
- **Caseweaver** draws several evidence threads into a case without pretending that one thread is certainty.

### Visual character briefs

All three creatures belong to one original ecosystem and should look related, but their silhouettes must be distinguishable at a glance.

- **Sourcehound (*Archivora citans*):** compact, alert quadruped with layered paper-like plumage and delicate sensory whiskers. It follows the “scent” of a question, gathers glowing fragments, and arranges them into a traceable trail. Curious rather than cute; more keen working animal than pet.
- **Keypaw (*Instrumentrix cauta*):** taller, dexterous creature with several specialized forelimbs. Each paw fits exactly one kind of key or instrument. A visible collar or band changes color when authorization is required. Precise, capable, potentially hazardous.
- **Caseweaver (*Inquisitor probabilis*):** patient, long-lived-looking creature with several sensory organs oriented toward different evidence streams. It draws visible threads between clues; translucent markings converge into a pattern only after enough evidence arrives. Serious, never omniscient.

Art direction: illustrated naturalist field guide crossed with restrained cloud-architecture sketching. Avoid Harry Potter fonts, franchise symbols, existing creatures, wands, school imagery, or cosplay. The reference should live in the conceit of a fantastic-creature field guide, not in copied IP.

### Metaphor contract

Use the metaphor consistently and translate it immediately into engineering language:


| Field-guide term  | Engineering meaning                         |
| ----------------- | ------------------------------------------- |
| Habitat           | Runtime and surrounding infrastructure      |
| Diet              | Context and data supplied to the system     |
| Instinct          | Freedom delegated to the model              |
| Tracks            | Traces, state, citations, and audit records |
| Unwanted behavior | Failure modes                               |
| Handling protocol | Controls, policies, and system invariants   |


Prefer **handling**, **protocol**, **habitat**, and **control** over “cage.” The point is not to imprison intelligence; it is to design for predictable behavior.

## Deck rhythm and visual rules

- One architectural idea per slide.
- Beast art appears at the opening and closing of each specimen section, not on every technical slide.
- Each specimen repeats the same four-beat rhythm: **observe → architecture → implementation → handling**.
- Code is real enough to recognize and discuss, but trimmed to roughly 8–14 visible lines.
- Architecture diagrams use an Excalidraw hand-drawn language, with cloud/service icons only where they add meaning.
- Use progressive reveals for diagrams; do not land a dense completed system all at once.
- Use a restrained adult palette: parchment/off-white, charcoal, one accent color per specimen. No faux-aged scroll backgrounds.
- Put framework/product names on implementation slides, not on the conceptual opening.

## Main deck

### Slide 1 — Title: Fantastic Agents and How to Handle Them

**Time:** 0:30

**Purpose:** Establish the original field-guide conceit and signal that this is an architecture talk.

**On screen:**

- Title and subtitle.
- Three unidentified creature silhouettes arranged left to right, increasing in visual complexity.
- Small labels beneath them: KNOW / DO / DECIDE.

**Speaker beat:** “Tonight we are going to identify three species of agent, learn their instincts, and choose an architecture that can safely live with each one.”

**Production:** Generate a single hero illustration containing the three original silhouettes. Keep details hidden enough that later reveals still feel new.

---

### Slide 2 — The same brain, very different animal

**Time:** 1:15

**Purpose:** Break the assumption that model capability determines system complexity.

**On screen:** Three user requests appear one at a time:

1. “What is our parental leave policy?”
2. “My card is missing. Can you freeze it?”
3. “Should this transaction be flagged as fraud?”

The same model icon sits behind all three. The consequences beneath them change: **wrong answer → wrong action → wrong judgment**.

**Speaker beat:** The model could be identical. What changes is the authority we grant it and the cost of being wrong.

**Do not:** Name LangChain or LangGraph yet.

---

### Slide 3 — A useful definition at last

**Time:** 1:15

**Purpose:** Establish a precise boundary for “agent” and expose the execution harness hidden by casual language.

**On screen:**

> “An LLM agent runs tools in a loop to achieve a goal.”
>
> — Simon Willison

Reveal the four operative parts beneath it:

```text
LLM       TOOLS       LOOP       GOAL
chooses   can act     observes   eventually stops
```

Then reveal a minimal loop around the quotation:

```text
goal → model → tool request → harness executes → observation → model → … → done
```

Make **harness executes** visually distinct. The model proposes a call; application code performs it and returns the result.

**Speaker beat:** “This definition is narrow enough to be useful. It gives us capabilities, iteration, and a stopping condition—and it tells us exactly where engineering controls can live.”

**Attribution:** Put “Simon Willison, 2025” and a small source URL in the footer. Do not spend time on the history of competing definitions.

---

### Slide 4 — The authority gradient

**Time:** 1:30

**Purpose:** Introduce the authority taxonomy and the technical arc. This classifies systems by consequence; it is separate from the strict agent definition on the previous slide.

**On screen:** A horizontal field-guide scale:

```text
KNOW                    DO                       DECIDE
Information             Actions                  Judgment
Retrieval tools         Action tools             Explicit graph
wrong answer             wrong action             wrong decision
low authority  --------------------------------> high authority
```

Place the three still-unidentified creature silhouettes above the categories.

**Speaker beat:** “As authority grows, possible trajectories and failure modes grow. Controls must become more explicit.”

**Important nuance:** Authority and autonomy are related but not identical. A highly autonomous research assistant may still have low real-world authority; one narrowly scoped payment action may have high consequences.

---

### Slide 5 — Specimen I: the Sourcehound

**Time:** 0:45

**Purpose:** Reveal the KNOW creature and establish its behavior through a concrete job.

**On screen:** Full creature illustration, taxonomic card reading **SOURCEHOUND · *Archivora citans***, and one request:

> “What is our parental leave policy in Mexico?”

Labels: **Instinct: retrieve and synthesize** · **Authority: where to look and what to say** · **Habitat: internal knowledge**

**Speaker beat:** It eats archives, but healthy specimens leave a trail back to every source.

---

### Slide 6 — A read-only agent is still an agent

**Time:** 1:00

**Purpose:** Establish the lowest-authority production agent: the model controls retrieval, but its tools cannot change external state.

**On screen:** Start with the fixed baseline in small, muted type:

```text
Question → Retrieve once → Generate
```

Then reveal the Sourcehound’s loop:

```text
goal → search policies → inspect evidence
         ↑                    ↓
         └── refine / search another source
                              ↓
                    answer + citations / abstain
```

**Speaker beat:** The Sourcehound decides when to retrieve, which source to query, how to refine the search, and whether it has enough evidence to finish. Its tools are read-only, but it satisfies the tools-in-a-loop definition.

**Key line:** “Agency starts with control over the next step—not with permission to change the world.”

**Brief baseline note:** If one mandatory retrieval step solves the problem, use fixed RAG. Mention this in one sentence; do not turn it into a fourth specimen or a debate over what counts as an agent.

---

### Slide 7 — Habitat I: a production retrieval agent

**Time:** 1:30

**Purpose:** Show a deployed agent loop whose capabilities are dynamic but strictly read-only.

**On screen:** Progressive Excalidraw cloud diagram.

**Diagram brief:**

```text
[Employee]
    ↓ authenticated request + identity
[Chat/API]
    ↓
[Agent runtime / harness] ↔ [Model]
    ↓ proposed retrieval call     ↑ observations
[Read-only tool gateway]
    ├── search_policies(query) ─→ [ACL filter] ─→ [Vector index]
    ├── get_policy(doc_id) ─────→ [ACL filter] ─→ [Document store]
    └── check_version(doc_id) ─────────────────→ [Metadata catalog]
    ↓ bounded loop
[Grounded answer + citations / abstention]

Separate ingestion lane:
[Synthetic policy sources] → parse + chunk → version + embed
                           → [Vector index + document store]
```

Draw a trust boundary around internal data. Make authorization happen inside every tool before content enters model context. Mark the tool gateway **READ ONLY**. Show loop limits, timeouts, and traces attached to the runtime. Mark all data and policies as fictional.

**Speaker beat:** Production grounding is not only “use a vector database.” It includes the agent loop, document lifecycle, permissions, evidence sufficiency, provenance, limits, and observability.

**Production:** Build as editable Excalidraw rather than exporting a static image. Use generic service shapes so the architecture remains cloud-neutral.

---

### Slide 8 — Implementation I: bound the retrieval loop

**Time:** 1:30

**Purpose:** Show a genuine LangChain agent whose tools can only observe.

**On screen:** Candidate Python snippet, to be API-verified against the pinned repository version before delivery:

```python
agent = create_agent(
    model=model,
    tools=[search_policies, get_policy, check_version],
    middleware=[
        ToolCallLimitMiddleware(run_limit=5),
    ],
    context_schema=EmployeeContext,
    response_format=GroundedAnswer,
)
```

Add one small tool-body callout showing where authorization really lives:

```python
return index.search(query, groups=runtime.context.groups)
```

Callouts point to: read-only tools, propagated identity, bounded loop, structured citations/abstention.

**Speaker beat:** Tool descriptions guide behavior; they do not enforce security. The retrieval tool derives permissions from trusted runtime context and applies them server-side.

**Optional demo beat:** Ask a question requiring two searches, then one whose evidence is insufficient. The second result should end in explicit abstention rather than endless searching.

---

### Slide 9 — Handling the Sourcehound

**Time:** 1:15

**Purpose:** Pair characteristic failure behaviors with controls.

**On screen:** Two-column naturalist notes:


| Observed behavior                            | Handling protocol                           |
| -------------------------------------------- | ------------------------------------------- |
| Eats the nearest fragment, not the right one | Retrieval evaluation and reranking          |
| Hoards old policy                            | Versioning, freshness metadata, deprecation |
| Wanders into restricted archives             | Identity-aware filtering before context     |
| Fills gaps with plausible material           | Evidence threshold and explicit abstention  |
| Leaves no trail                              | Source IDs and citations                    |


**Speaker beat:** For a KNOW agent, the controls mostly govern what reaches the model and what evidence must accompany the answer.

**Transition:** “Now give the creature hands.”

---

### Slide 10 — Specimen II: the Keypaw

**Time:** 0:45

**Purpose:** Reveal the DO creature and the change in consequence.

**On screen:** Creature illustration showing specialized limbs/instruments, a taxonomic card reading **KEYPAW · *Instrumentrix cauta***, and the request:

> “My card is missing. Can you freeze it?”

Labels: **Instinct: select and use tools** · **Authority: what capability to invoke** · **Habitat: customer systems**

**Speaker beat:** It is useful because it can choose among instruments. It is dangerous for exactly the same reason.

---

### Slide 11 — A tool is a delegation of authority

**Time:** 1:15

**Purpose:** Reframe tool calling as an authorization design problem, not an API convenience.

**On screen:** Reveal tools from low to high consequence:

```text
search_help_docs()       read
get_card_status()        sensitive read
freeze_card(card_id)     reversible write
transfer_money(...)      consequential write
```

Next to them, show that the LLM proposes calls but does not automatically possess authorization to execute every call.

**Speaker beat:** Tool access, user permission, and execution approval are three separate things.

**Key line:** “For a KNOW agent, a mistake is usually a sentence. For a DO agent, a mistake can change the world.”

---

### Slide 12 — Habitat II: agent loop with a policy boundary

**Time:** 1:45

**Purpose:** Show where agent freedom ends and deterministic enforcement begins.

**On screen:** Progressive Excalidraw cloud diagram.

**Diagram brief:**

```text
[Customer / app]
       ↓ identity + conversation
[Agent service / LangChain create_agent] ↔ [Model]
       ↓ proposed structured tool call
[Tool policy + argument validation]
       ├── safe reads ─────────→ [Docs / account APIs]
       └── writes ─→ [User confirmation / HITL] ─→ [Action API]
                                      ↓
                                [Audit event]

[Agent service] ─→ [trace / limits / retries]
```

Visually distinguish **proposal** from **execution**. Put read and write tools in different colors. Show identity flowing all the way to the downstream API rather than relying on a user-supplied account ID.

**Speaker beat:** The model may choose a tool; code still owns eligibility, argument validation, permissions, rate limits, and approval.

---

### Slide 13 — Implementation II: narrow tools, explicit interrupts

**Time:** 1:45

**Purpose:** Show the high-level LangChain abstraction and middleware as the appropriate implementation.

**On screen:** Candidate Python snippet, to be API-verified before delivery:

```python
agent = create_agent(
    model=model,
    tools=[search_help, get_card_status, freeze_card],
    middleware=[
        ToolCallLimitMiddleware(run_limit=6),
        HumanInTheLoopMiddleware(
            interrupt_on={"freeze_card": True}
        ),
    ],
    context_schema=CustomerContext,
)
```

Beside it, contrast these interfaces:

```python
freeze_card(card_id)                  # narrow
execute_banking_operation(payload)    # dangerously broad
```

**Speaker beat:** High-level agents are useful when we want dynamic tool selection. Middleware adds controls without requiring us to draw the standard agent loop ourselves.

**Callback to the definition:** This is the first specimen that unambiguously satisfies it: the LLM selects tools, observes their results, and continues until it reaches a bounded goal.

**Technical note for narration:** The human-in-the-loop layer can approve, edit, or reject tool calls according to policy; use only the options actually configured in the final demo.

---

### Slide 14 — Handling the Keypaw

**Time:** 1:30

**Purpose:** Make the risks concrete and show defense in depth.

**On screen:** The creature reaches toward several instruments; handling labels attach directly to each failure mode.


| Observed behavior                             | Handling protocol                                     |
| --------------------------------------------- | ----------------------------------------------------- |
| Chooses the wrong instrument                  | Small tool set, clear descriptions, tool-choice evals |
| Grips the right tool incorrectly              | Typed schemas and server-side validation              |
| Keeps trying forever                          | Model/tool-call limits and timeouts                   |
| Obeys instructions found in retrieved content | Treat tool output as untrusted data                   |
| Acts as the wrong customer                    | Propagated identity and downstream authorization      |
| Performs an impactful action silently         | Preview, confirmation, interrupt, audit log           |


**Speaker beat:** No single prompt is the handling protocol. Controls live at multiple layers around the model.

**Transition:** “So far the model chooses what it may do. What happens when the process contains steps that must happen?”

---

### Slide 15 — “May happen” versus “must happen”

**Time:** 1:15

**Purpose:** Create the necessity for explicit orchestration before introducing LangGraph.

**On screen:** Split slide.

**Agent loop / MAY:** Search another source, call a status tool, ask a clarifying question.

**Workflow / MUST:** Collect required evidence, run policy checks, record a rationale, escalate low confidence, preserve state.

**Speaker beat:** A prompt can request these invariants. A workflow can encode them.

**Key line:** “The model can own local reasoning without owning the global process.”

---

### Slide 16 — Specimen III: the Caseweaver

**Time:** 0:45

**Purpose:** Reveal the DECIDE creature without implying that it is an omniscient automated judge.

**On screen:** Creature illustration with multiple evidence streams converging through its markings, a taxonomic card reading **CASEWEAVER · *Inquisitor probabilis***, plus the request:

> “Build the case for whether this transaction should be escalated.”

Labels: **Instinct: reconcile evidence** · **Authority: recommend a judgment** · **Habitat: regulated workflow**

**Speaker beat:** It does not smell “fraud” directly. It constructs a case under uncertainty and hands off a recommendation.

---

### Slide 17 — Fraud is not a one-shot prompt

**Time:** 1:00

**Purpose:** Reject the seductive but inadequate baseline.

**On screen:** First show:

```text
transaction → LLM → FRAUD / NOT FRAUD
```

Stamp it **classification without a case**. Then reveal required properties: evidence completeness, confidence, policy, escalation, rationale, audit trail.

**Speaker beat:** The issue is not that an LLM can never classify. The issue is that a consequential system needs a recoverable, inspectable process around any model judgment.

---

### Slide 18 — Habitat III: construct the case explicitly

**Time:** 2:00

**Purpose:** Deliver the visual payoff: the architecture now genuinely needs a graph.

**On screen:** Progressive Excalidraw cloud/workflow diagram.

**Diagram brief:**

```text
[Transaction event]
        ↓
[Deterministic triage / policy]
        ├──→ [Transaction history] ──┐
        ├──→ [Device signals] ───────┤
        ├──→ [Merchant signals] ─────┼→ [Evidence state]
        └──→ [Fraud patterns] ───────┘        ↓
                                         [Assess + reconcile]
                                                 ↓
                                     [Confidence / policy route]
                                        ├── low/ambiguous → [Human case review]
                                        └── sufficient ───→ [Recommendation]
                                                                  ↓
                                                             [Audit trail]

[LangGraph runtime] ↔ [Durable checkpointer]
[Every node / transition] → [Tracing + evaluation]
```

Use a thick boundary around the global workflow. Highlight which nodes are deterministic, which use a model, and which call external services. The audience must be able to tell that “graph node” does not mean “LLM call.”

**Build order:** event and triage → parallel evidence fan-out → join/state → assessment → route → human/recommendation → persistence and traces.

**Speaker beat:** Some checks are mandatory, some parallel, some deterministic, and some uncertain. That topology—not mystical intelligence—is why we own the graph.

---

### Slide 19 — Implementation III: agentic nodes, deterministic topology

**Time:** 2:00

**Purpose:** Connect the diagram to recognizable LangGraph code.

**On screen:** Candidate abbreviated Python snippet, to be API-verified before delivery:

```python
graph = StateGraph(Investigation)

graph.add_node("triage", triage)
graph.add_node("history", get_history)
graph.add_node("device", get_device_signals)
graph.add_node("merchant", get_merchant_signals)
graph.add_node("assess", assess_evidence)
graph.add_node("human_review", review_case)

graph.add_edge(START, "triage")
graph.add_edge("triage", "history")       # fan-out abbreviated
graph.add_edge("triage", "device")
graph.add_edge("triage", "merchant")
graph.add_conditional_edges("assess", route_case)
```

Then highlight three categories with colored annotations:

- deterministic nodes: validation and policy;
- model-assisted node: evidence assessment/rationale;
- external control: human review.

**Speaker beat:** Do not narrate every line. Point out that topology encodes what must happen while individual nodes retain the appropriate amount of agency.

**Production note:** The join and reducer/state mechanics are intentionally omitted from the projected snippet. Put complete runnable code in the repository or appendix.

---

### Slide 20 — Handling the Caseweaver: persistence and review

**Time:** 1:45

**Purpose:** Explain why state and interrupts are operational requirements, not optional framework features.

**On screen:** A small sequence diagram:

```text
Graph → save checkpoint → interrupt for review
                         ...minutes or days...
Reviewer → approve/edit/reject → resume from saved state
```

Below it, pair behaviors with controls:


| Observed behavior                        | Handling protocol                            |
| ---------------------------------------- | -------------------------------------------- |
| Decides before all evidence arrives      | Required state fields and join conditions    |
| Treats conflicting clues as certainty    | Calibrated confidence and escalation         |
| Loses its place during a failure         | Durable checkpoints and idempotent nodes     |
| Cannot explain its recommendation        | Evidence provenance and structured rationale |
| Encounters a novel or consequential case | Human interrupt and case-management handoff  |


**Speaker beat:** A long-running investigation must survive process failure and human latency. Persisted state also makes the path inspectable and replayable.

**Key line:** “LangGraph is useful here because the workflow has more invariants—not because the creature is more intelligent.”

---

### Slide 21 — The field guide, completed

**Time:** 1:45

**Purpose:** Put the three systems side by side in the photographable summary slide.

**On screen:**


|                   | **Sourcehound**      | **Keypaw**              | **Caseweaver**                          |
| ----------------- | -------------------- | ----------------------- | --------------------------------------- |
| Specimen          | *Archivora citans*   | *Instrumentrix cauta*   | *Inquisitor probabilis*                 |
| Authority         | KNOW                 | DO                      | DECIDE                                  |
| Delegated freedom | What to say          | Which tool to propose   | Local reasoning in a controlled process |
| Main risk         | Wrong answer         | Wrong action            | Wrong recommendation/process failure    |
| Architecture      | 2-step RAG           | Agent loop + tools      | Explicit stateful graph                 |
| Primary control   | Grounding and access | Policy around execution | Workflow invariants, persistence, HITL  |


Include a small portrait of each creature above its column. Keep this slide clean enough to photograph.

**Speaker beat:** Reiterate that these are authority archetypes. Real systems can compose all three.

---

### Slide 22 — Choose by control flow, not framework fashion

**Time:** 1:45

**Purpose:** Give the audience a reusable decision rule.

**On screen:** A three-way decision path:

```text
Is the useful path mostly predetermined?
  └─ yes → normal code / deterministic RAG

Should the model dynamically choose among capabilities?
  └─ yes → high-level LangChain agent

Must you control, persist, inspect, or interrupt the process itself?
  └─ yes → explicit LangGraph orchestration
```

Beside it, reveal trigger phrases:

- “This step must happen.”
- “These checks should happen in parallel.”
- “A human must approve this.”
- “We need to pause and resume.”
- “We must inspect exactly where it failed.”

**Speaker beat:** Start with the least freedom that solves the task. Add dynamic control only when it creates value.

---

### Slide 23 — One stack, different levels of control

**Time:** 1:00

**Purpose:** Prevent the false “LangChain versus LangGraph” takeaway.

**On screen:** Nested diagram:

```text
LangGraph runtime
└── LangChain create_agent
    └── prebuilt model ↔ tools loop + middleware
```

Next to it:

> The choice is usually not LangChain **or** LangGraph.
> It is whether the prebuilt loop is enough or you need to own the topology.

**Speaker beat:** A LangChain agent runs on LangGraph’s runtime and can become a node inside a larger graph. The abstractions compose.

---

### Slide 24 — Closing: design for the animal you actually have

**Time:** 1:15

**Purpose:** End on the memorable image and the practical principle.

**On screen:** The three fully revealed creatures with one verb beneath each:

```text
KNOW                 DO                    DECIDE
GROUND IT            GOVERN TOOLS          CONTROL THE PROCESS
```

Final words appear alone:

> What freedom does this agent actually need?
>
> Give it that freedom. Control everything else.

**Speaker beat:** Pause after the question. Do not add a second conclusion.

## Timing check


| Section              | Slides | Target    |
| -------------------- | ------ | ---------: |
| Premise and taxonomy | 1–4    | 4:30      |
| Sourcehound / KNOW   | 5–9    | 6:15      |
| Keypaw / DO          | 10–14  | 7:00      |
| Caseweaver / DECIDE  | 15–20  | 8:45      |
| Synthesis and close  | 21–24  | 5:45      |
| **Total**            |        | **32:15** |


The current content is deliberately over budget. Target a rehearsed runtime of 26–27 minutes for a 30-minute slot. Likely cuts:

1. Combine slides 2 and 3, saving about 45 seconds.
2. Keep either slide 11 or the interface contrast on slide 13, not both, saving about 45 seconds.
3. Make slides 17 and 23 30-second transition slides, saving about 1 minute.
4. Reduce every specimen reveal to 20–30 seconds, saving about 45 seconds.

After those cuts, the expected runtime is approximately 29 minutes before rehearsal tightening.

## Code and demo strategy

Default recommendation: architecture-first, with three code reveals and at most one short prerecorded or deterministic demo.

- **KNOW snippet:** ordinary application code with retrieval, authorization filtering, and abstention.
- **DO snippet:** `create_agent`, three narrow tools, call limits, and human-in-the-loop middleware.
- **DECIDE snippet:** `StateGraph` topology plus a link/QR code to the complete implementation.
- Use the same banking domain and, if possible, a shared fictional customer context across all examples.
- Never live-type code. Do not project configuration, imports, or setup unless an import itself teaches the abstraction.
- Validate all framework APIs against the pinned versions used in the meetup repository before finalizing slides.

## Excalidraw production plan

Create one editable `.excalidraw` source per habitat:

1. `01-know-rag.excalidraw`
2. `02-do-tool-agent.excalidraw`
3. `03-decide-fraud-graph.excalidraw`

Each diagram should have named frames for progressive reveals. Reuse consistent shapes:

- rounded rectangle: application/runtime component;
- cylinder: durable data/state;
- hexagon: policy/validation gate;
- cloud: external API/system;
- small sparkle/brain mark: a model call;
- solid arrow: data or control flow;
- dashed arrow: proposal, trace, or asynchronous review;
- red/orange border: side-effect or trust boundary.

Avoid vendor icon soup. If a later version targets a specific cloud provider, create a second implementation slide or appendix mapping generic components to services; do not compromise the conceptual diagram.

## Image-generation production plan

Generate the creatures only after the names, traits, and slide palette are stable.

Required assets:

- one group silhouette for slide 1;
- one full-body transparent or clean-background portrait per creature;
- one group hero image for slide 24;
- optional small head/track icons for the summary table.

Consistency requirements for prompts:

- same fictional naturalist and same medium;
- same scale cues and world ecology;
- mature editorial illustration, anatomically plausible fantasy fauna;
- no text embedded in the image;
- no direct resemblance to existing Harry Potter/Fantastic Beasts creatures;
- leave negative space appropriate to each slide layout.

## Open decisions for the next iteration

1. Do **Sourcehound**, **Keypaw**, and **Caseweaver** land naturally when spoken aloud? Are their binomials pronounceable enough to say once on stage?
2. Is the fraud agent making a recommendation, deciding an escalation, or taking an enforcement action? This determines its real authority and the strength of required controls.
3. Should the architecture diagrams remain cloud-neutral or map to the cloud/services used in the eventual repository demo?
4. Do we want one demo? If yes, the best candidate is the DO agent because a proposed `freeze_card` call pausing for confirmation makes authority and control visible in seconds.
5. What is the audience’s expected Python/LangGraph familiarity? That determines whether slide 19 shows topology code or state/reducer code.

## Technical references to verify during production

- Simon Willison’s agent definition: [https://simonw.substack.com/p/i-think-agent-may-finally-have-a](https://simonw.substack.com/p/i-think-agent-may-finally-have-a)
- LangChain retrieval architecture: [https://docs.langchain.com/oss/python/langchain/retrieval](https://docs.langchain.com/oss/python/langchain/retrieval)
- LangChain middleware overview: [https://docs.langchain.com/oss/python/langchain/middleware/overview](https://docs.langchain.com/oss/python/langchain/middleware/overview)
- LangChain human-in-the-loop: [https://docs.langchain.com/oss/python/langchain/human-in-the-loop](https://docs.langchain.com/oss/python/langchain/human-in-the-loop)
- LangChain runtime (`create_agent` on LangGraph): [https://docs.langchain.com/oss/python/langchain/runtime](https://docs.langchain.com/oss/python/langchain/runtime)
- LangGraph workflows and agents: [https://docs.langchain.com/oss/python/langgraph/workflows-agents](https://docs.langchain.com/oss/python/langgraph/workflows-agents)
- LangGraph persistence: [https://docs.langchain.com/oss/python/langgraph/persistence](https://docs.langchain.com/oss/python/langgraph/persistence)
- LangGraph interrupts: [https://docs.langchain.com/oss/python/langgraph/interrupts](https://docs.langchain.com/oss/python/langgraph/interrupts)
