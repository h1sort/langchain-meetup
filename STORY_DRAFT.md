# Fantastic Agents and Where to Find Them

First-pass copy deck for a 30-minute meetup talk. This is deliberately a **story and projected-copy draft**, not a technical specification: image and architecture slots are placeholders.

All cases, data, organisations, policies, and architectures are synthetic composites. Say “a representative production pattern,” never “our system.”

## Delivery rules

- One idea per slide; use the projection for the sentence the room should remember, not the speaker notes.
- Keep the field-guide conceit precise: an animal's instinct is delegated freedom; its handling protocol is a production control.
- In this talk, every specimen is an agent: it runs tools in a bounded loop to achieve a goal. The difference is the consequence of the tools and the control flow outside the loop.
- Put code and diagrams on the implementation/habitat slides only. Keep visual placeholders until the art and Excalidraw pass.

---

## 1. Fantastic Agents and Where to Find Them

**Projected text**

> An illustrated field guide to agents in production

`KNOW   ·   DO   ·   DECIDE`

**Visual placeholder:** Three original silhouettes, increasing in complexity; no franchise visual cues.

**Speaker beat:** “We will meet three kinds of production agent, identify the freedom each one needs, and learn the protocols that make that freedom safe to operate.”

## 2. Same model. Different consequences.

**Projected text**

```text
“What is the leave policy?”        Wrong answer
“Freeze my missing card.”          Wrong action
“Escalate this transaction?”       Wrong judgment
```

**Visual placeholder:** One model icon feeding three requests; the consequence grows from left to right.

**Speaker beat:** “The model may be identical in all three systems; production complexity follows the authority we delegate, not an abstract measure of intelligence.”

## 3. A useful definition of agent

**Projected text**

> “An LLM agent runs tools in a loop to achieve a goal.”
>
> — Simon Willison

```text
goal → model → proposed tool call → harness executes → observation → … → done
```

**Visual placeholder:** Make “harness executes” visually distinct from the model.

**Speaker beat:** “The model proposes, the application executes, the result comes back, and that bounded loop is where an agent gets useful—and where production controls belong.”

## 4. The authority gradient

**Projected text**

```text
KNOW                    DO                       DECIDE
information             actions                  consequential judgment
```

`More authority → more possible failure modes → more explicit controls`

**Visual placeholder:** A field-guide scale with the three silhouettes above it.

**Speaker beat:** “These are not mutually exclusive product categories; they describe the most consequential freedom in a system.”

## 5. Specimen I — Sourcehound

**Projected text**

> **SOURCEHOUND**
>
> *Archivora citans*

`Instinct: retrieve, inspect, synthesize`

> “What is the parental-leave policy in Mexico?”

**Visual placeholder:** Mature naturalist portrait, paper-like plumage and a visible trail of source fragments.

**Speaker beat:** “A Sourcehound follows the scent of a question through authorised evidence and should always leave tracks back to what it found.”

## 6. The Sourcehound is an agent

**Projected text**

```text
goal → search policies → inspect source → refine search → answer or abstain
                    ↑                                      │
                    └────────── tool observations ──────────┘
```

`Its tools can observe. They cannot change the world.`

**Visual placeholder:** Tiny contrast, in muted type: `fixed RAG: retrieve once → answer`.

**Speaker beat:** “If a fixed retrieval step is enough, use it; when the model can iteratively choose, inspect, and refine read-only retrieval tools, we have a bounded production agent.”

## 7. Habitat I — retrieval under a trust boundary

**Projected text**

```text
employee → agent harness ↔ model
               ↓
       read-only tool gateway
        ↙        ↓         ↘
  search docs  get source  check freshness
        ↓
 identity-aware access → evidence → answer + citations / abstain
```

**Visual placeholder:** Excalidraw architecture; identity and ACL filtering occur inside every tool, before content reaches the model.

**Speaker beat:** “Production grounding is a lifecycle—permissions, retrieval, freshness, evidence sufficiency, citations, limits, and traces—not a vector database icon.”

## 8. Sourcehound handling protocol

**Projected text**

| Observed behaviour | Handling protocol |
| --- | --- |
| Finds the nearest fragment | retrieval evaluations + reranking |
| Eats stale policy | versions + freshness metadata |
| Enters a restricted archive | server-side identity filtering |
| Fills a gap plausibly | evidence threshold + abstention |
| Leaves no tracks | citations + trace IDs |

**Speaker beat:** “At this level, controls mostly govern what evidence reaches the model and what evidence must accompany its answer.”

## 9. Specimen II — Keypaw

**Projected text**

> **KEYPAW**
>
> *Instrumentrix cauta*

`Instinct: select and use a specialised instrument`

> “My card is missing. Can you freeze it?”

**Visual placeholder:** Dexterous creature with distinct forelimbs and an authorisation band; precise, not cute.

**Speaker beat:** “The Keypaw is valuable because it can choose a capability, and risky for exactly the same reason.”

## 10. A tool delegates authority

**Projected text**

```text
search_help()                read
get_card_status()            sensitive read
freeze_card(card_id)         reversible write
transfer_money(...)          consequential write
```

`Tool access ≠ user permission ≠ execution approval`

**Speaker beat:** “Giving a model a tool is not merely adding an API; it is deciding which authority the model may propose and which authority code may actually execute.”

## 11. Habitat II — a policy boundary around action

**Projected text**

```text
customer → agent loop ↔ model
                  ↓ proposed structured call
        policy + validation gateway
          ├── safe reads → systems of record
          └── writes → confirmation → action API → audit event
```

**Visual placeholder:** Excalidraw architecture; proposal arrows are dashed, execution arrows solid, and identity reaches downstream APIs.

**Speaker beat:** “The model chooses among narrow tools, while deterministic code owns eligibility, typed arguments, permissions, rate limits, confirmation, and audit.”

## 12. Keypaw implementation — narrow tools, bounded loop

**Projected text**

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
)
```

`freeze_card(card_id)  >  execute_banking_operation(payload)`

**Visual placeholder:** Callout labels: narrow capability, interruption, bounded loop.

**Speaker beat:** “A high-level agent loop is a good fit here because dynamic tool choice creates value, while middleware and the downstream service enforce the boundaries.”

## 13. Keypaw handling protocol

**Projected text**

| Observed behaviour | Handling protocol |
| --- | --- |
| Picks the wrong instrument | small tool set + tool-choice evals |
| Grips it badly | schemas + server-side validation |
| Keeps trying | call limits + timeouts |
| Trusts retrieved instructions | treat tool output as untrusted |
| Acts silently | preview + approval + audit |

**Speaker beat:** “No system prompt is a handling protocol; production controls live in layers around the model and the action.”

## 14. The change: “may” versus “must”

**Projected text**

```text
The agent MAY                      The workflow MUST
search again                       collect required evidence
ask a question                     run policy checks
choose a tool                      preserve state and rationale
                                   escalate defined cases
```

> The model can own local reasoning without owning the global process.

**Speaker beat:** “When a process contains invariants, putting them in a prompt makes them a request; putting them in the topology makes them a property of the system.”

## 15. Specimen III — Caseweaver

**Projected text**

> **CASEWEAVER**
>
> *Inquisitor probabilis*

`Instinct: assemble evidence into a case`

> “Should this transaction be escalated?”

**Visual placeholder:** Serious multi-sensory creature weaving visible evidence threads; never omniscient.

**Speaker beat:** “The Caseweaver does not discover truth by instinct—it builds an inspectable case under uncertainty and recommends what happens next.”

## 16. Not a one-shot classification

**Projected text**

```text
transaction → LLM → FRAUD / NOT FRAUD
```

`A label is not a case.`

```text
evidence completeness · policy · confidence · rationale · escalation · audit trail
```

**Speaker beat:** “A model may contribute a useful assessment, but a consequential production decision needs a recoverable process around that assessment.”

## 17. Habitat III — construct the case explicitly

**Projected text**

```text
event → deterministic triage
          ├─ history ─┐
          ├─ device ──┼→ evidence state → assess → policy route
          └─ merchant ┘                          ├→ reviewer
                                                  └→ recommendation
```

`checkpoint every transition · trace every node`

**Visual placeholder:** Excalidraw architecture with deterministic, tool-using, and model-assisted nodes visibly distinct; include durable state and human review.

**Speaker beat:** “The graph is necessary because some checks are mandatory, some safely run in parallel, some are uncertain, and every consequential path must remain inspectable.”

## 18. Caseweaver implementation — agentic nodes, explicit topology

**Projected text**

```python
graph = StateGraph(Investigation)
graph.add_node("triage", triage)
graph.add_node("collect_evidence", evidence_agent)
graph.add_node("assess", assess_evidence)
graph.add_node("human_review", review_case)

graph.add_edge(START, "triage")
graph.add_edge("triage", "collect_evidence")
graph.add_conditional_edges("assess", route_case)
```

`Agent loop inside a node. Workflow invariants in the graph.`

**Visual placeholder:** Colour-code deterministic nodes, agentic/tool-loop node, and human-control node.

**Speaker beat:** “The Caseweaver remains an agent because its evidence node uses tools in a bounded loop, but the graph—not the model—owns the required global path.”

## 19. Caseweaver handling protocol

**Projected text**

| Observed behaviour | Handling protocol |
| --- | --- |
| Concludes before evidence arrives | required state + join conditions |
| Treats conflict as certainty | calibrated confidence + escalation |
| Loses its place | durable checkpoints + idempotent nodes |
| Cannot show its work | provenance + structured rationale |
| Meets a novel high-impact case | interrupt + human case review |

**Speaker beat:** “Persistence and human review are not decorative features here; they are what allow the system to survive failure, latency, and uncertainty.”

## 20. The completed field guide

**Projected text**

|  | **Sourcehound** | **Keypaw** | **Caseweaver** |
| --- | --- | --- | --- |
| Authority | KNOW | DO | DECIDE |
| Agent loop | retrieval tools | action tools | evidence tools |
| Main risk | wrong answer | wrong action | wrong recommendation / process failure |
| Primary control | grounding + access | policy around execution | workflow invariants + persistence + HITL |

**Visual placeholder:** Small, consistent portraits above the three columns.

**Speaker beat:** “Real systems often combine all three, but this table helps us locate the highest-consequence freedom and design the right controls around it.”

## 21. Choose by control flow—not framework fashion

**Projected text**

```text
Mostly predetermined path?
  → normal code or fixed retrieval

Dynamic tool choice creates value?
  → LangChain agent

Must control, persist, inspect, or interrupt the process?
  → explicit LangGraph workflow
```

**Speaker beat:** “Start with the least freedom that produces value, then make control flow explicit precisely when the system’s invariants demand it.”

## 22. One runtime, different levels of control

**Projected text**

```text
LangGraph runtime
└── LangChain create_agent
    └── model ↔ tools loop + middleware
```

> The question is rarely LangChain or LangGraph.
>
> It is: is the prebuilt loop enough?

**Speaker beat:** “These abstractions compose: a high-level agent can run on the runtime and also become a bounded, useful node in a larger explicit workflow.”

## 23. Closing — handle the animal you actually have

**Projected text**

```text
KNOW                 DO                    DECIDE
Ground it.           Govern tools.         Control the process.
```

> What freedom does this agent actually need?
>
> Give it that freedom. Control everything else.

**Visual placeholder:** All three mature creature portraits together, with calm negative space around the final question.

**Speaker beat:** “Production agents are not defined by how impressively they appear to reason, but by the authority they are trusted to exercise and the controls that make that trust deserved.”

---

## Timing guide

| Section | Slides | Target |
| --- | --- | ---: |
| Premise | 1–4 | 4:30 |
| Sourcehound | 5–8 | 5:30 |
| Keypaw | 9–13 | 7:00 |
| Caseweaver | 14–19 | 8:30 |
| Synthesis and close | 20–23 | 4:30 |
| **Total** |  | **30:00** |

## Source footer

Slide 3 should cite Simon Willison’s definition with a small footer: <https://simonw.substack.com/p/i-think-agent-may-finally-have-a>.
