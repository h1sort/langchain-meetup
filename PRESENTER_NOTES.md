# Fantastic Agents and Where to Find Them

## Presenter notes for a 30-minute talk

These notes follow the current 23-slide `fantastic-agents.html` deck. They are deliberately more detailed than the final delivery should be. Treat them as rehearsal material: absorb the argument, preserve the key phrases that feel natural, and then speak to the room rather than reading a script.

All scenarios, architectures, systems, users, and data in the talk are teaching composites based on real-world patterns, with details altered for privacy. Never describe a diagram as “our architecture.” Use “a representative production architecture.”

## The argument in one minute

An LLM agent runs tools in a loop to achieve a goal. The model proposes tool calls; an application harness executes them—or refuses to execute them.

The same model can inhabit systems with very different authority:

- **Sourcehound / Know:** chooses which evidence to inspect and synthesizes an answer.
- **Keypaw / Do:** chooses which capability to propose, potentially creating a side effect.
- **Caseweaver / Decide:** performs bounded, local investigation inside a workflow that contributes to a consequential judgment.

As authority and consequence increase, controls become more explicit. Retrieval requires grounding, authorization, limits, and citations. Actions require governed execution, approval where appropriate, idempotency, reconciliation, and audit. Consequential judgment requires required evidence, explicit routes, durable state, calibrated thresholds, and human review.

The recurring principle is:

> Give the model local discretion where it creates value. Keep permissions, invariants, and consequences in deterministic systems around it.

The closing question is:

> What freedom does this agent actually need?

## Timing plan

Aim to deliver the prepared material in about **26½ minutes**, leaving 3–4 minutes for a slower room, spontaneous emphasis, or questions.

| Section | Slides | Target |
| --- | ---: | ---: |
| Premise, definition, taxonomy | 1–4 | 3:45 |
| Sourcehound — Know | 5–9 | 5:10 |
| Keypaw — Do | 10–14 | 5:10 |
| May versus must | 15 | 1:00 |
| Caseweaver — Decide | 16–20 | 6:35 |
| Synthesis, decision guide, close | 21–23 | 3:35 |
| Natural pauses and transitions | — | 1:15 |
| **Planned total** | — | **26:30** |

The slides are not equal units. Spend time on the definition, the three architecture diagrams, “may versus must,” and the final decision guide. The specimen reveals should be quick.

---

## Slide 1 — Fantastic Agents and Where to Find Them

**Target:** 0:35
**Purpose:** Establish the field-guide device, the production focus, and the privacy boundary.

### Suggested delivery

“This is a field guide to production agents. We are going to meet three fictional specimens: one that knows, one that does, and one that contributes to decisions.

The creature names are a memory device. The engineering underneath is the point.

One note before we begin: these examples are teaching composites based on real-world patterns, with details altered for privacy. Nothing here represents a particular company, customer, architecture, or incident.

By the end, I want you to have one question you can take into a design review: **what is the most consequential freedom we are delegating?**”

### Key phrases

- “The creature names are a memory device; the engineering underneath is the point.”
- “Know. Do. Decide.”
- “What is the most consequential freedom we are delegating?”

### Visual walk

Point to the title, then move once across **Know → Do → Decide**. Do not explain the animals yet. Let the visual create curiosity.

### Delivery notes

- Keep the tone lightly playful, then move quickly into serious engineering.
- Do not explain the invented Latin names.
- Mention the privacy framing once, calmly. It should feel transparent rather than defensive.

### Transition

“The reason I begin with authority is that the same underlying model can lead to very different consequences.”

### Optional cut

“Three fictional production patterns: Know, Do, Decide. They are privacy-preserving teaching composites. Let’s start with why the distinction matters.”

---

## Slide 2 — The hard part is not the brain

**Target:** 1:00
**Purpose:** State the central thesis: production architecture follows consequence, not merely model capability.

### Suggested delivery

“Imagine placing the same capable model in three different jobs.

On the left, someone asks for a policy answer. A bad outcome is a wrong or unauthorized answer. That matters, but the immediate system consequence is usually informational.

In the middle, someone asks to freeze a missing card. Now the central failure is not that the model says the wrong thing. It is that the system performs the wrong action, perhaps for the wrong customer.

On the right, the model contributes to whether a transaction should be escalated. That is a judgment inside a process. Evidence completeness, rationale, uncertainty, policy, and review all matter.

The model can be identical in all three. The consequence is not.

The hard part is usually not making the model sound intelligent. The hard part is deciding what it may initiate, what deterministic systems must enforce, and how the application recovers when the model is wrong.”

### Key phrases

- “The model can be identical. The consequence is not.”
- “A wrong answer, a wrong action, and a wrong judgment are different engineering problems.”
- “Architecture follows consequence.”

### Visual walk

Read the three examples left to right, then point to the failure line under each. Land on the sentence at the bottom.

### Nuance

“Decide” does not mean the model necessarily makes an unreviewed final decision. In this deck, Caseweaver contributes a recommendation inside a governed process.

### Transition

“Before we classify these systems, I want to be precise about what I mean by an agent.”

### Optional cut

State the three failures and the final thesis only.

---

## Slide 3 — A useful definition of agent

**Target:** 1:10
**Purpose:** Establish the governing definition and expose the production harness.

### Suggested delivery

“I use Simon Willison’s definition because it is compact and operational:

> An LLM agent runs tools in a loop to achieve a goal.

That gives us four useful things: a model, tools, iteration, and an eventual stopping condition.

It also excludes some fuzzy uses of the word. A model answering once from context is not automatically an agent. A deterministic retrieval pipeline is not automatically an agent. Under this definition, the model has to be able to choose a tool, observe the result, and use that observation to choose what happens next.

But the most important step on this diagram is not ‘Model.’ It is **Harness executes**.

The model proposes a tool call. The harness executes it—or refuses. The harness carries trusted identity, applies policy, enforces budgets and deadlines, records traces, and decides how failure is handled.

Prompts guide behavior. Applications enforce behavior.”

### Key phrases

- “An LLM agent runs tools in a loop to achieve a goal.”
- “The model proposes; the harness executes.”
- “Prompts guide behavior. Applications enforce behavior.”
- “Tools, loop, goal, stopping condition.”

### Visual walk

1. Deliver the quote slowly.
2. Trace **Goal → Model → Tool request**.
3. Pause on **Harness executes**.
4. Continue through **Observation** and the loop back.
5. Finish at the stopping-condition footer.

### Nuance

- This is an operational definition for discussing production controls, not a philosophical claim about agency.
- Do not say the model “executes” the tool. That language hides the most important engineering boundary.
- If asked whether RAG is an agent: “Not necessarily. It becomes an agent under this definition when the model dynamically selects retrieval tools in a loop.”

### Transition

“With that boundary in place, we can classify agents by the most consequential freedom their tool loop is allowed to exercise.”

### Optional cut

Keep the exact quote, “the model proposes; the harness executes,” and the prompt-versus-enforcement distinction.

---

## Slide 4 — The authority gradient

**Target:** 1:00
**Purpose:** Introduce the three archetypes without presenting them as rigid categories or maturity levels.

### Suggested delivery

“The question across the top is the classification rule: **what is the most consequential freedom you delegate?**

Sourcehound can decide which evidence tool to use and how to synthesize an answer: Know.

Keypaw can select a capability and propose an action: Do.

Caseweaver can perform local reasoning that contributes to a consequential judgment inside a controlled process: Decide.

As we move to the right, we are not necessarily using a smarter model. We are accepting more ways for the system to create an operational problem. The controls therefore become more explicit: authorization, stopping conditions, observability, persistence, evaluation, and human oversight.

These are archetypes, not boxes. A real system may contain all three. Classify it by the highest-consequence freedom it contains, not by the safest thing it does most often.

And authority is not the same thing as autonomy. A research agent may run for an hour but have only read access. One narrow payment action may be brief but highly consequential.”

### Key phrases

- “Classify by the highest-consequence freedom.”
- “More failure modes require more explicit controls.”
- “Authority is not autonomy.”
- “These are archetypes, not boxes.”

### Visual walk

Move left to right across the three columns, then trace the authority scale. Finish with the bottom explanatory sentence.

### Transition

“Let’s begin with the lowest-authority specimen—not control-free, but bounded in a particularly useful way.”

### Optional cut

Omit the autonomy example and return to it during questions.

---

# Specimen I — Sourcehound / Know

## Slide 5 — Sourcehound

**Target:** 0:35
**Purpose:** Make the retrieval archetype memorable and define its authority boundary.

### Suggested delivery

“This is Sourcehound: *Archivora citans*, a tracker of evidence.

Its job is not simply to retrieve one block of text. It can investigate: search a policy collection, inspect a source, check whether the version is current, and decide whether it needs another pass.

Its authority is intentionally narrow. It can choose where to look and what to say, but it cannot change the world.

Its health signal is a visible trail. The user should be able to see which authorized sources support the answer.”

### Key phrases

- “Follow the evidence. Leave the trail.”
- “It can choose where to look and what to say; it cannot change the world.”
- “Healthy specimens cite their tracks.”

### Visual walk

Point to the creature, the Mexico-specific question, then the three facts: instinct, authority, habitat.

### Transition

“The important word is ‘investigate.’ That tells us when retrieval earns an agent loop and when ordinary RAG is enough.”

---

## Slide 6 — Agency starts with the next step

**Target:** 0:55
**Purpose:** Distinguish dynamic agentic retrieval from fixed RAG.

### Suggested delivery

“A fixed RAG path may always retrieve once, assemble context, and answer. That is often exactly the right architecture. If one mandatory retrieval step reliably solves the job, use normal RAG. Simpler systems are easier to evaluate, debug, and operate.

Sourcehound becomes an agent when the model can ask: ‘These results are incomplete. Should I refine the query? Inspect another source? Check a policy version? Or admit that the evidence is insufficient?’

The next step depends on the last observation. That is the loop.

Notice the boundary: the model can steer retrieval, but its tools are read-only. It cannot update the policy, grant itself access, or create an external side effect.”

### Key phrases

- “Agency starts with the next step.”
- “If one mandatory retrieval step solves the job, normal RAG is enough.”
- “Use an agent loop only when dynamic investigation adds value.”
- “Read-only does not mean control-free.”

### Visual walk

Trace **Question → search policies → inspect evidence → answer + citations**, then point to the return loop for refinement.

### Nuance

Several deterministic pipeline stages do not make a system agentic. The model must be choosing tools based on prior observations.

### Transition

“Read-only gives us a valuable boundary, but it does not solve authorization, stale evidence, leakage, or endless investigation. This is the habitat around the loop.”

---

## Slide 7 — Sourcehound habitat

**Target:** 1:30
**Purpose:** Show that authorized, attributable evidence must cross a deterministic gateway before reaching model context.

### How to frame the diagram

Say: “I’m going to trace one request, not narrate every arrow.”

### Suggested delivery

“This is a representative production architecture.

Start on the left. An employee application sends an authenticated request. The important input is not only the question. It includes trusted, server-attested context: subject, groups, role, and perhaps jurisdiction.

Inside the bounded retrieval-agent zone, the model can iterate between itself and read tools. But this is not an unlimited conversation with the archive. We set model-turn and tool-call budgets, a wall-clock deadline, and an explicit safe exit: cited answer or abstention.

The model proposes a search. The **read-only retrieval gateway** executes it. That gateway combines the query with trusted identity claims and applies ACL filtering before any evidence enters model context.

This is the security boundary to remember: the model never decides which protected documents it is allowed to see. A prompt cannot grant access. A tool argument cannot grant access. Authorization belongs in the retrieval path.

On the right, only authorized evidence returns, carrying a source ID and version. The agent returns an answer with citations, or it abstains.

Along the bottom are two different kinds of evidence about the agent itself. The trace and citation ledger tells us what happened in one run: tool calls, source IDs, denials, and latency. The offline evaluation suite tells us whether the system is generally healthy: access leakage, freshness, and citation support.”

### Key phrases

- “Authorization before model context.”
- “The model proposes a search; the gateway executes an authorized search.”
- “The model may choose the next search. Trusted application context decides which evidence may cross the boundary.”
- “Traces explain a run. Evaluations tell us whether the system is generally healthy.”

### Visual walk

1. Employee application and authenticated request.
2. Bounded agent: **Model ↔ Read tools**.
3. Budgets, deadline, and abstention.
4. Trusted identity context.
5. Read-only retrieval gateway and ACL.
6. Index and document store.
7. Authorized evidence with source ID and version returning to the agent.
8. Cited answer or abstention returning to the user.
9. Trace ledger.
10. Offline evaluation.

### What not to overexplain

- Do not explain the choice of index technology or storage product.
- Do not narrate each wire separately.
- Do not imply ACL filtering belongs in the prompt or is performed by the model.

### Transition

“The framework code is short because the critical implementation point is not the `create_agent` call. It is what the runtime and the tools enforce around it.”

### Optional cut

Compress the bottom rail to: “We trace individual runs and evaluate the system offline.” Never cut identity, ACL-before-context, or bounded execution.

---

## Slide 8 — Sourcehound implementation

**Target:** 1:15
**Purpose:** Make the retrieval controls concrete while preventing API syntax from becoming the lesson.

### Suggested delivery

“This is deliberately a small implementation slice, not a full application.

The agent receives only three read tools: search policies, inspect a selected policy, and check its version.

The model-call and tool-call limits are separate. They control different failure modes: too many reasoning turns versus excessive tool activity. In a real service I would also have wall-clock deadlines and timeouts at every downstream boundary.

`EmployeeContext` carries trusted runtime context. The important enforcement is shown inside the tool: the search uses groups supplied by the runtime, not a group list written by the model, copied from the user message, or smuggled in through a tool argument.

One subtle but important point: `response_format=GroundedAnswer` makes the output structure predictable. It can require citation fields. It does not prove that those citations support the answer.

Schema validates shape. Application code verifies source identity, access, citation presence, and evidence sufficiency. Evaluations tell us how often those checks lead to a useful, supported answer.”

### Key phrases

- “A tool description is not authorization.”
- “Trusted identity travels in runtime context.”
- “Schema validates shape; code verifies citations.”
- “Budgets are stopping controls, not grounding controls.”

### Visual walk

1. Narrow tool list.
2. Model-call limit.
3. Tool-call limit and explicit error behavior.
4. Context schema and response format.
5. Right-side headline and three notes.
6. `runtime.context.groups` inside the tool.

### Nuance

- Six and five are illustrative budgets, not universal recommendations.
- A tool-call limit alone is not a complete hard execution deadline.
- Structured output is useful for validation and downstream handling; it is not epistemic proof.

### Transition

“When we look at Sourcehound’s characteristic failures, we can see why read-only is not the same as control-free.”

### Optional cut

Skip exact middleware semantics; preserve runtime identity and schema-versus-grounding.

---

## Slide 9 — Sourcehound handling protocol

**Target:** 0:55
**Purpose:** Turn the metaphor into a reusable retrieval-production checklist.

### Suggested delivery

“Sourcehound is a hound, not a historian. It is optimized to pursue the next promising fragment. It does not independently guarantee complete, current, authorized truth.

So every predictable behavior gets a corresponding control.

Nearest fragment: retrieval evaluation and reranking.

Old policy: versioning, freshness metadata, and deprecation.

Restricted archive: identity-aware filtering before context.

Plausible gap: an evidence threshold and explicit abstention.

Circular investigation: model and tool budgets plus deadlines.

No trail: source IDs and citations.

The general pattern is important: do not ask a prompt to make the animal safe. Design the habitat and handling protocol around its predictable behavior.”

### Key phrases

- “It is a hound, not a historian.”
- “Every predictable behavior gets a corresponding control.”
- “Do not ask a prompt to make an animal safe; design the habitat around it.”

### Visual walk

Do not read every cell mechanically. Spend most time on:

- Restricted archive → identity filtering.
- Plausible gap → evidence threshold and abstention.
- Sniffs in circles → budgets and deadlines.
- Leaves no trail → citations.

### Nuance

Abstention should be useful: state what evidence is missing, offer a safe next step, or route to a human-supported process.

### Transition

“So far, the creature can only look. Now let’s give it hands.”

---

# Specimen II — Keypaw / Do

## Slide 10 — Keypaw

**Target:** 0:30
**Purpose:** Escalate from information access to side-effect proposals.

### Suggested delivery

“This is Keypaw: *Instrumentrix cauta*.

The user request sounds ordinary: ‘My card is missing. Can you freeze it?’

But the system is no longer deciding only what to say or where to look. It can propose a change in a customer system.

Keypaw is useful because it can choose among capabilities—and hazardous for exactly the same reason.

The correct mental model is not ‘a chatbot with an API.’ It is an action system that happens to use a model for local reasoning.”

### Key phrases

- “It can open things.”
- “Useful and hazardous for the same reason.”
- “An action system that uses a model for local reasoning.”

### Transition

“Once a tool can act, the tool itself becomes a delegation of authority.”

---

## Slide 11 — A tool delegates authority

**Target:** 0:55
**Purpose:** Separate model tool access, user entitlement, and execution approval.

### Suggested delivery

“A tool is not merely an API convenience. It is delegated authority described in a form the model can select.

These functions look similar in code, but their consequences are very different.

Searching help documentation is a read. Looking up card status is a sensitive read. Freezing a card is a reversible write. Transferring money is a consequential write.

The fact that a tool appears in the model’s available vocabulary does not mean the current user is entitled to use it. And even if the user is entitled, that does not mean this invocation is approved to execute immediately.

So keep these three concepts separate:

**Tool access is not user permission. User permission is not execution approval.**

The model proposes. Deterministic policy and downstream services decide whether the proposal becomes an action.”

### Key phrases

- “A tool is a delegation of authority.”
- “Tool access ≠ user permission ≠ execution approval.”
- “The model proposes; deterministic services decide.”

### Visual walk

Move down the tool list as an authority ladder. Pause at `freeze_card` and again at `transfer_money`.

### Nuance

Never ask the model to decide whether a user is entitled. Permission comes from trusted identity, policy, and authoritative downstream services.

### Transition

“That distinction becomes visible in the architecture. We need a policy boundary around execution, not merely a more careful prompt.”

---

## Slide 12 — Keypaw habitat

**Target:** 1:35
**Purpose:** Show how a typed proposal becomes a governed, replay-safe transaction.

### How to frame the diagram

“Again, I’m tracing one request. The important distinction is proposal versus execution.”

### Suggested delivery

“The customer application begins with a trusted session, identity, and consent. We do not treat the model’s interpretation of who the customer is as identity.

Inside the bounded action-agent zone, the model sees a small set of narrow tools. It has turn budgets and tool deadlines. It emits a **typed proposal**: for example, `freeze_card` with a particular card identifier and reason.

At this point, nothing has changed in the world.

The policy decision evaluates the proposal using deterministic facts and rules: identity, entitlement, schema validity, risk, and rate limits.

Safe reads can follow their governed read path. A write that requires approval goes to a preview and interrupt. The customer or reviewer sees the exact effect and parameters before choosing approve or reject.

Only then does the execution broker act. The broker carries a single-use approval token and an idempotency key. The authoritative system of record performs the operation and returns a receipt or outcome.

The bottom rail is the part cheerful demos omit. We record the proposal, policy result, approver, idempotency key, receipt, and timeout state. If a network timeout leaves the final outcome uncertain, we reconcile with the system of record before retrying. We do not casually repeat a write because the first request looked slow.”

### Key phrases

- “Proposal is not execution.”
- “Preview the exact effect and parameters.”
- “The system of record remains authoritative.”
- “Timeout means reconcile before retry.”
- “A human approval cannot grant an authority the user does not have.”

### Visual walk

1. Customer app.
2. Trusted identity and consent.
3. Bounded action agent: Model ↔ narrow tools.
4. Typed proposal.
5. Policy decision.
6. Safe-read path briefly.
7. Preview and interrupt.
8. Customer or reviewer.
9. Execution broker with single-use token and idempotency key.
10. System of record and receipt.
11. Audit and reconciliation rail.

### Nuance

- Approval is proportional to risk and reversibility; not every read or reversible action requires a human.
- Approval is not a replacement for authorization.
- Idempotency protects against duplicate execution, but uncertain outcomes still require reconciliation.
- The model must never call the side-effecting system of record directly.

### Transition

“Framework middleware can pause the local agent loop, but that pause is meaningful only when the surrounding execution contract is designed correctly.”

### Optional cut

Compress the safe-read branch to one sentence. Preserve policy → preview → approval → broker → system of record → reconciliation.

---

## Slide 13 — Keypaw implementation

**Target:** 1:15
**Purpose:** Demonstrate a narrow action agent with explicit budgets, persisted interruption, and resumption.

### Suggested delivery

“This code shows the bounded local loop, not the whole policy or banking system.

The model receives only three tools. The tool set is deliberately narrow because narrow operations are easier to describe, authorize, validate, evaluate, audit, and recover.

Model-call and tool-call limits bound different dimensions of execution. The human-in-the-loop middleware marks `freeze_card` as an interruption point. When the model proposes that call, the graph pauses before execution.

For that pause to survive reality, the state must be persisted through a production checkpointer. When an authorized decision arrives, the application resumes the same execution using the same stable thread identity.

But be precise about what this middleware gives us. It creates a controlled pause. It does not replace entitlement checks, typed validation, downstream authorization, idempotency, reconciliation, or audit.

The design principle on the right is the important part: give it a key for one narrowly described operation, not a master key for an arbitrary banking operation.”

### Key phrases

- “Give it a key, not a master key.”
- “Interrupt before execution; resume the same persisted thread.”
- “Human-in-the-loop is one control in a larger execution contract.”
- “Narrow tools are easier to authorize, validate, evaluate, and recover.”

### Visual walk

1. Tool list.
2. Model and tool budgets.
3. HITL block.
4. Persistent checkpointer.
5. Narrow `freeze_card` versus broad arbitrary-operation function.
6. Stable-thread note.

### Nuance

Avoid presenting this as a complete production implementation. Identity propagation, policy evaluation, execution brokering, timeouts, audit, and reconciliation exist outside this snippet.

### Transition

“That is the implementation pattern. The handling protocol shows what can still go wrong when the creature has hands.”

### Optional cut

Explain the semantic behavior—narrow tool, interrupt, persistence, resume—without reading middleware names.

---

## Slide 14 — Keypaw handling protocol

**Target:** 0:55
**Purpose:** Close the action section by framing safety as an end-to-end system property.

### Suggested delivery

“Once the agent has hands, control the behavior around the tool call.

If it chooses the wrong instrument, keep the tool set small and evaluate tool selection.

If it uses the right tool incorrectly, enforce typed schemas and server-side validation.

If a write is repeated, rely on idempotency keys and reconciliation—not hope.

If an API result, document, or ticket contains hostile instructions, treat tool output as untrusted input. External text does not get to rewrite system policy.

If it acts as the wrong customer, propagate trusted identity and enforce authorization again downstream.

If it acts silently, provide preview, confirmation where appropriate, and a durable audit event.

There is no single magic prompt here. The protocol is a property of the entire system.”

### Key phrases

- “Control the behavior around the tool call.”
- “The protocol is a system property.”
- “Treat tool output as untrusted input.”
- “Idempotency is a safety control.”
- “There is no single magic prompt.”

### Visual walk

Highlight four pairs rather than reading all six:

- Wrong tool → small tool set and evaluations.
- Duplicate write → idempotency and reconciliation.
- Wrong customer → identity and downstream authorization.
- Silent action → preview, confirmation, audit.

### Transition

“So far, we have allowed the model to decide which local step is useful. Now we reach the turning point: what happens when some steps are not merely useful, but mandatory?”

---

## Slide 15 — May happen versus must happen

**Target:** 1:00
**Purpose:** Make the conceptual turn from a flexible agent loop to explicit workflow invariants.

### Suggested delivery

“Sourcehound may search another source. Keypaw may ask a clarifying question or propose a permitted tool. That flexibility is why we use the model.

But some steps are obligations rather than preferences.

The system may be required to collect particular evidence, run policy checks, record a rationale, or escalate uncertainty.

A prompt can say: ‘Always collect all required evidence first.’ That is a request to the model.

A workflow can make required evidence a precondition of assessment. That is a property of the system.

This is the design move: let the model own local reasoning where adaptation creates value. Keep global process explicit wherever the process carries an operational invariant.

We are not replacing intelligence with a graph. We are deciding which parts of the process are allowed to be optional.”

### Key phrases

- “A prompt can request an invariant. A workflow can encode one.”
- “May happen versus must happen.”
- “Let the model own local reasoning. Keep the global process explicit.”
- “Topology, not mysticism.”

### Visual walk

Point first to two items in **Agent loop · may**, then cross deliberately to **Workflow · must**. Land on **Escalate uncertainty**.

### Nuance

A graph does not make the model more intelligent. It makes process requirements enforceable, inspectable, and recoverable.

### Transition

Pause briefly, then say: “When the output contributes to a consequential judgment, this distinction becomes the architecture.”

---

# Specimen III — Caseweaver / Decide

## Slide 16 — Caseweaver

**Target:** 0:35
**Purpose:** Introduce the decision-workflow archetype and bound it to recommendation rather than autonomous verdict.

### Suggested delivery

“This is Caseweaver: *Inquisitor probabilis*, a patient pattern-maker of evidence.

Its job is not to pronounce a verdict. Its job is to build an inspectable case: gather the relevant evidence, preserve provenance, reconcile agreement and conflict, and produce a recommendation with a rationale.

The authority is bounded. Caseweaver contributes to judgment; it does not silently make the final world-changing decision.

Build a case, not a verdict.”

### Key phrases

- “Build a case, not a verdict.”
- “Contribute to judgment.”
- “Serious; never omniscient.”

### Visual walk

Point to the evidence threads, the escalation question, then instinct, authority, and habitat.

### Transition

“That boundary matters because the tempting baseline is much smaller—and much less defensible.”

---

## Slide 17 — Classification without a case

**Target:** 1:05
**Purpose:** Contrast a seductive one-shot label with a recoverable evidence process.

### Suggested delivery

“Here is the seductive baseline: transaction in, model out, fraud or not fraud.

It is attractive because it is compact, fast, and sometimes genuinely sufficient for low-consequence triage.

But it loses the things an operator, reviewer, or auditor needs when the answer is challenged.

What evidence did it use? Was the required evidence complete? Was it authorized to access it? Which policy threshold applied? What happened if a dependency failed halfway through? Why did this case pass while a similar one escalated?

For consequential work, the output must live inside a recoverable process: evidence completeness before assessment, validated thresholds and policy routing, structured rationale with provenance, escalation, and a durable audit trail.

The output is a recommendation—not a naked label.

That surrounding process is not bureaucracy around the product. It is the product.”

### Key phrases

- “Classification without a case.”
- “Consequential work needs a recoverable process.”
- “The output is a recommendation, not a naked label.”
- “That is not bureaucracy around the product. It is the product.”

### Visual walk

Start with the one-line classifier at left. Pause on the binary result. Move through the four production requirements on the right.

### Nuance

- Do not imply that models must never classify. The claim is proportional: a single classification is insufficient as the entire consequential decision system.
- A structured rationale is an inspectable claim tied to evidence, not proof that the conclusion is correct.

### Transition

“Now let’s look at the habitat: where agency lives, where deterministic control begins, and what survives after a failure.”

---

## Slide 18 — Caseweaver habitat

**Target:** 2:10
**Purpose:** Explain why the workflow exists and make the bounded inner agent loop unmistakable.

### How to frame the diagram

“This is the densest diagram. I’m going to explain it in four beats: scope, collect, assess, route.”

### Suggested delivery

“First: **scope**.

A transaction arrives, but it does not go directly to a model. Deterministic triage and case policy establish service identity, case scope, which lookups are permitted, and which evidence is required for this kind of case.

Second: **collect**.

History, device, merchant, and pattern evidence can be gathered in parallel. Each result is more than text. It carries provenance: source, timestamp, scope, and ideally the version or query that produced it.

Those branches meet at an evidence join. The join is the invariant. Assessment cannot begin until the required evidence is complete—or the workflow takes an explicit safe-failure route. The model cannot waive the requirement because it feels confident.

Third: **assess**.

Only here do we enter a bounded assessment agent. This box genuinely satisfies our definition: the model can inspect the case, call a case-scoped evidence tool, observe the result, and decide whether another inspection is useful.

Its discretion is local: three turns, a deadline, and read-only, case-scoped tools. It cannot expand its own permissions or change the required process.

Fourth: **route**.

The agent produces a structured assessment. Deterministic policy code evaluates evidence completeness, validated thresholds, and policy criteria. It routes either to a recommendation with rationale or to human review, where the workflow can interrupt and later resume.

Along the bottom is the operational substrate: durable case state, checkpoints, idempotency keys, and traces. The evaluation path uses replay, measured outcomes, reviewer overrides, and drift monitoring.

This is the central design: **local agency, global invariants**.”

### Key phrases

- “Scope, collect, assess, route.”
- “The join is the invariant.”
- “The model does not get to waive required evidence because it feels confident.”
- “The assessment agent has bounded local discretion inside the workflow.”
- “Local agency; global invariants.”
- “The graph exists because the process has invariants.”

### Visual walk

1. Transaction.
2. Service identity and case scope.
3. Triage and case policy.
4. Sweep over the four parallel evidence nodes without describing each service.
5. Evidence join: pause on **complete? provenance**.
6. Bounded assessment-agent boundary.
7. Model, case tools, and the loop arrow.
8. Three turns and deadline.
9. Deterministic route.
10. Recommendation.
11. Human review and interrupt/resume.
12. Durable-state rail.
13. Replay and calibration evaluation.

### Technical nuance

- The workflow graph is not automatically an agent. Caseweaver qualifies because the assessment node contains a model–tool loop.
- Parallel branches require reducer-safe state. Append provenance-tagged evidence or use separate state fields; do not let concurrent branches overwrite one another.
- A durable checkpointer enables recovery only if the backing store survives process failure.
- Checkpointing does not prevent duplicate external effects. Retryable nodes and downstream writes still require idempotency or reconciliation.
- “Confidence” should not mean the model’s self-reported feeling. Operational routing should use held-out calibration, evidence completeness, policy criteria, and monitored outcomes.

### What not to overexplain

- Do not define every evidence system.
- Do not explain Pregel or graph supersteps unless asked.
- Do not imply human review is a universal destination; it is a policy route proportional to uncertainty and consequence.

### Transition

“The implementation boundary fits into one sentence: the assessment is an agent; the route is code.”

### Optional cut

Summarize the parallel nodes as “least-privilege evidence collection.” Never cut the evidence join, inner agent boundary, deterministic route, or durable state.

---

## Slide 19 — Caseweaver implementation

**Target:** 1:25
**Purpose:** Translate the architecture into LangChain/LangGraph composition.

### Suggested delivery

“At the top, we create a small bounded LangChain agent called `assessor`.

It has one deliberately narrow case-evidence tool, a structured assessment response, and a three-model-call budget. That `create_agent` call supplies the actual agent loop: model, tool choice, tool execution, observation, then model again until it stops.

We then install the whole assessor as one node inside an explicit LangGraph workflow.

The graph owns the topology: triage first, fan out to evidence nodes, join after the required branches, assess, then follow deterministic conditional edges.

Notice the division of labor. The agent may decide how to inspect an already-authorized case bundle. The graph decides that triage happens first, mandatory evidence must arrive, and policy owns the route.

Finally, the graph compiles with a durable checkpointer. That gives the case a persistent execution history and makes later review and resume possible.

A graph is not automatically an agent. This node is.”

### Key phrases

- “The assessment is an agent. The route is code.”
- “A graph is not automatically an agent; this node is.”
- “Local agency, deterministic topology.”
- “The workflow owns the topology around the loop.”

### Visual walk

1. `assessor = create_agent(...)`.
2. Narrow evidence tool.
3. Model-call budget.
4. `graph.add_node("assess", assessor)`.
5. Fan-out loop.
6. Join edge.
7. Conditional route.
8. Durable checkpointer.

### Technical nuance

- LangChain’s `create_agent` runs on the LangGraph runtime. The choice is not LangChain *or* LangGraph; this example embeds a prebuilt agent graph inside a larger explicit workflow.
- Real code must define adapters between outer workflow state and the assessor’s expected state.
- Parallel state updates need reducers. Ordering should be explicit if it matters.
- A model-call limit is a budget, not the whole reliability story. Production code still needs downstream timeouts, overall deadlines, and safe behavior when budgets are exhausted.
- Structured output validates shape, not evidence truth.

### Transition

“The durable checkpointer matters because serious work does not finish neatly inside one uninterrupted HTTP request.”

### Optional cut

Point only to the assessor node, conditional route, and checkpointer. Say: “Agent inside; invariant topology outside.”

---

## Slide 20 — Pause, review, resume

**Target:** 1:20
**Purpose:** Explain persistence and human oversight as durable workflow states.

### Suggested delivery

“Long-running work has to survive reality.

A reviewer goes offline. A dependency fails. A case is novel. Policy says automation must stop.

The pattern is explicit: the graph reaches a review point, saves a checkpoint, interrupts, and stops. It may remain paused for minutes or days. When an authorized reviewer submits a decision, the application resumes the same stable thread from durable state.

A review is not just a UI modal. It is a state transition with a durable record: which evidence existed, what the agent recommended, why policy escalated, who reviewed it, what they overrode, and what happened next.

The table is the operating checklist.

Assessment before evidence: required state fields and joins.

Conflict mistaken for certainty: thresholds validated on held-out outcomes and escalation.

Failure midway through the case: durable checkpoints and idempotent nodes.

Unexplainable recommendation: provenance and structured rationale.

Novel case: human interruption and a captured override label for future evaluation.

Graph does not mean more intelligent. Graph means more invariants.”

### Key phrases

- “Pause. Review. Resume.”
- “Long-running work must survive reality.”
- “A review is not just a UI modal. It is a durable state transition.”
- “Graph ≠ more intelligent; graph = more invariants.”

### Visual walk

Trace the pause/resume sequence first. Then select three table rows:

- Evidence completeness.
- Held-out calibration and escalation.
- Durable recovery or novel-case review.

### Technical nuance

- A stable thread ID and persistent checkpointer are required for long-lived resumption.
- Resumption consumes a concrete reviewer decision, not merely a repeat of the original request.
- Interruptible nodes must be idempotent because resumption and retry may re-enter work.
- Reviewer overrides are useful evaluation signals, but they require quality control before being treated as ground truth.
- Calibration means measured performance against outcomes, not a model-generated confidence number.

### Transition

“We now have the complete field guide. The next slide is the one to photograph rather than trying to memorize every middleware call.”

---

## Slide 21 — The completed field guide

**Target:** 1:10
**Purpose:** Consolidate the taxonomy into an operational comparison.

### Suggested delivery

“Here is the whole field guide in one frame.

Sourcehound has the authority to know. It chooses evidence tools. Its central controls are grounding, access, limits, and citations.

Keypaw has the authority to do. It proposes actions. Its central control is governed execution: policy, approval where appropriate, idempotency, reconciliation, and audit.

Caseweaver contributes to a consequential judgment. Its local reasoning lives inside a controlled process. Its central controls are workflow invariants, durable state, evaluation, and human oversight.

Do not treat these as mutually exclusive product categories. A Caseweaver workflow may contain Sourcehound-style evidence retrieval. A Keypaw action may sit behind a Caseweaver review route.

The production discipline is consistent: **name the authority, then name the control**.”

### Key phrases

- “Three archetypes, one production discipline.”
- “Name the authority. Then name the control.”
- “Systems can combine all three.”
- “Controls should grow with the consequence of delegated freedom.”

### Visual walk

Do not read the entire matrix. Read only one row across at a time:

1. Delegated freedom.
2. Main risk.
3. Primary control.

### Nuance

This is not a maturity ladder. A complex research agent can remain read-only; a very narrow action can carry high consequence.

### Transition

“So how do we choose the architecture without turning this into framework fashion?”

### Optional cut

State only the three primary controls and the headline.

---

## Slide 22 — Architecture decision guide

**Target:** 1:30
**Purpose:** Give the audience a practical architecture and framework-selection heuristic.

### Suggested delivery

“Start with the least freedom that creates value.

If the path is mostly predetermined—a fixed retrieval step, a known service sequence, normal application logic—use normal code. Determinism is a feature. You do not earn points for adding an agent loop.

If dynamic tool choice genuinely adds value, use a LangChain agent. That abstraction is appropriate when the model needs to decide which bounded, permitted tool to use next.

If the system must pause, persist, inspect, replay, recover, or enforce order, own the workflow topology explicitly in LangGraph.

This is rarely LangChain versus LangGraph. LangChain’s `create_agent` runs on the LangGraph runtime. A prebuilt agent can be embedded as a node in a larger graph, exactly as Caseweaver demonstrated.

The practical question is: **is the prebuilt loop enough, or do we need to own the topology around the loop?**

Normal code, a prebuilt agent, and an explicit workflow are composable tools—not competing identities.”

### Key phrases

- “Start with the least freedom that creates value.”
- “Choose by control flow, not fashion.”
- “Normal code is a valid answer.”
- “The choice is rarely LangChain or LangGraph.”
- “Do you need to own the topology around the loop?”

### Visual walk

1. Read the three questions top to bottom.
2. Point to the runtime stack only after explaining the decision path.
3. Trace **LangGraph runtime → LangChain create_agent → model ↔ tools loop + middleware**.

### Nuance

- A deterministic workflow can contain LLM calls without the whole system being an agent.
- Not every arbitrary LangGraph workflow is an agent.
- Middleware is excellent for local loop controls; global process requirements may be clearer as explicit nodes, edges, and policy services.

### Transition

“The closing principle is simpler than the technology choices.”

### Optional cut

For a framework-neutral audience, omit the runtime stack and keep the three architecture questions.

---

## Slide 23 — Closing

**Target:** 0:55
**Purpose:** Leave one memorable design principle and stop cleanly.

### Suggested delivery

Slow down and look at the audience rather than the slide.

“The final handling principle is: design for the animal you actually have.

For a hound: protect the evidence and require a trail.

For a creature with hands: govern the execution path—identity, policy, approval where appropriate, idempotency, and audit.

For a case-maker: make the process recoverable and reviewable—required evidence, explicit routes, durable state, and human oversight proportional to consequence.

The model may be the same. The authority is not. The controls should match the consequence.

What freedom does this agent actually need?

Give it that freedom. Control everything else.

Thank you.”

Pause. Do not add a new idea after the final line.

### Key phrases

- “Design for the animal you actually have.”
- “The model may be the same. The authority is not.”
- “The controls should match the consequence.”
- “Give it that freedom. Control everything else.”
- “Fantastic agents are still software systems.”

### Visual walk

Sweep once across **Ground it → Govern the tools → Control the process**. Then make eye contact for the final question and answer.

### Short closing if over time

“The model may be the same. The authority is not. Design for the animal you actually have: ground it, govern its tools, and control the process. Thank you.”

---

# Delivery guide

## How to explain every architecture diagram

Use the same four-part pattern:

1. **What enters the system?** Trusted request, identity, and scope.
2. **What may the model choose?** The bounded local discretion that earns the agent loop.
3. **Where does deterministic control apply?** Authorization, policy, joins, approval, or routing.
4. **What evidence remains afterward?** Citations, trace events, audit receipts, checkpoints, reviewer decisions, and evaluation labels.

Helpful phrases:

- “I’m going to trace one request, not every arrow.”
- “The important boundary is here.”
- “The model’s discretion stops here.”
- “This is an enforcement point, not another prompt.”
- “This rail is how we know what happened later.”
- “This is a representative production architecture.”

Avoid following arrows mechanically. The audience should leave with the control boundary, not a memorized component inventory.

## Core contrasts to repeat

### Proposal versus execution

> The model proposes. The application executes—or refuses.

### Guidance versus enforcement

> Prompts and tool descriptions guide model behavior. Deterministic code and downstream services enforce permissions and invariants.

### Shape versus truth

> Structured output validates shape. It does not prove that evidence supports a conclusion.

### Local versus global control

> Let the model own local reasoning. Keep global process explicit where it carries an invariant.

### Persistence versus idempotency

> A checkpoint preserves execution state. It does not by itself prevent a duplicate external effect.

### Trace versus evaluation

> A trace explains what happened in one run. Evaluation measures whether the system is generally safe and useful.

## Words and claims to avoid

Avoid: “The agent has permission.”
Prefer: “The application and downstream service authorize this execution.”

Avoid: “The model froze the card.”
Prefer: “The model proposed a typed freeze request; policy and approval authorized execution.”

Avoid: “Structured output guarantees grounding.”
Prefer: “Structured output validates shape; evidence checks validate support.”

Avoid: “The model is 92% confident.”
Prefer: “The route uses thresholds calibrated against held-out outcomes.”

Avoid: “Just add a human in the loop.”
Prefer: “Design an interruptible, persisted review path with exact evidence, identity, and safe resumption.”

Avoid: “Our architecture,” “our incident,” or “our data.”
Prefer: “A representative production architecture” and “a teaching composite based on real-world patterns.”

Avoid: “Graphs are safer” as a blanket statement.
Prefer: “Explicit topology can enforce order, persistence, routing, and review when those are requirements.”

Avoid: “Fully autonomous.”
Prefer: “Bounded local discretion” or “a governed tool loop.”

## Likely questions and concise answers

### “Are these really three different kinds of agents?”

They are production archetypes. The distinction is not model intelligence; it is the most consequential freedom the system delegates and the controls that freedom requires.

### “Is RAG an agent?”

Not necessarily. Under the definition used here, fixed retrieval followed by generation is not an agent. It becomes agentic when the model can choose retrieval tools repeatedly based on prior observations to achieve a goal.

### “Can’t the system prompt enforce these rules?”

Prompts shape model behavior. Application code and downstream services enforce access, state requirements, execution approval, idempotency, and routing. If a requirement must always hold, encode it outside the model.

### “Does human review make an action safe?”

Not by itself. Review must sit inside a system that preserves trusted identity, entitlement, exact intended effect, evidence, audit history, and a safe resume path. Approval cannot grant authority the user lacks.

### “Why use both model and tool-call limits?”

They bound different failure modes: excessive reasoning turns and excessive tool activity. Production systems should also apply wall-clock deadlines and downstream timeouts.

### “Does a checkpoint prevent duplicate actions?”

No. A checkpoint preserves workflow state. Side effects still need idempotency keys, deduplication, receipts, and reconciliation for uncertain outcomes.

### “Why parallel evidence collection?”

Independent evidence can be collected concurrently to reduce latency. The important part is the join: the workflow makes complete, provenance-tagged evidence a prerequisite for assessment.

### “Can the assessment model decide to escalate?”

It can emit signals and a structured recommendation. Deterministic policy should decide the operational route using evidence completeness, validated thresholds, and policy criteria.

### “Why use a graph instead of an agent?”

This example uses both. A bounded agent provides local tool choice inside the assessment node. An explicit graph owns mandatory collection, order, persistence, recovery, and routing around it.

### “Do we need LangGraph for every agent?”

No. Use the least topology necessary. A graph earns its complexity when pausing, persistence, inspection, recovery, or enforced order are genuine requirements.

### “How should these systems be evaluated?”

Evaluate the authority boundary:

- Retrieval: relevance, freshness, citation support, abstention, and access leakage.
- Action: tool selection, argument validity, policy denials, duplicate-write behavior, and recovery after uncertain outcomes.
- Decision workflow: evidence completeness, threshold calibration, escalation precision, reviewer overrides, process failures, and drift.

### “Where should a team start?”

Start with a narrow, observable, read-only capability with clear abstention. Add authority only when the team can name and implement the corresponding controls.

## Audience interaction and pacing

- Pause after the Simon Willison definition.
- Pause after “The model proposes; the harness executes.”
- Pause after “A prompt can request an invariant. A workflow can encode one.”
- On each habitat slide, turn toward the diagram, trace one path, and return your attention to the audience for the takeaway.
- Do not read code line by line. State what boundary the code proves.
- Do not read protocol tables in full. Select the two or three rows that reinforce the section’s thesis.
- Allow a beat after “Build a case, not a verdict.”
- Deliver the final question and answer more slowly than the rest of the talk.

## Emergency cuts

### If two minutes behind

- Reduce specimen slides 5, 10, and 16 to one sentence each.
- On slides 9 and 14, discuss only two handling rows.
- On slide 19, say only: “The assessment is an agent; the route is code.”
- On slide 21, state only the three primary controls.

Keep slides 3, 7, 12, 15, 18, 22, and 23. They carry the argument.

### If five minutes behind

Use this fast path:

1. Slides 1–4 in three minutes.
2. Sourcehound: slides 5–8; treat slide 9 as a ten-second summary.
3. Keypaw: slides 10–12; skip most code and protocol detail.
4. Keep slide 15 intact.
5. Caseweaver: slides 16–18 and 20; reduce slide 19 to one sentence.
6. Skip the detailed matrix walkthrough on slide 21.
7. Keep slides 22 and 23 intact.

The compressed thesis is:

> Read-only agents need evidence and access controls. Action agents need governed execution. Decision systems need explicit, recoverable workflow control.

## Final rehearsal checklist

- Can you state the agent definition exactly?
- Can you distinguish model proposal from application execution without looking at the slide?
- Can you explain each habitat diagram in four beats or fewer?
- Can you explain why Sourcehound is an agent but fixed RAG is not?
- Can you explain why HITL needs persisted state and a stable thread identity?
- Can you explain why Caseweaver is an agent even though its route is deterministic?
- Can you distinguish checkpointing from idempotency?
- Can you explain that calibration comes from measured outcomes, not model self-confidence?
- Can you deliver “may versus must” in under one minute?
- Can you close without adding a new idea after the final line?
