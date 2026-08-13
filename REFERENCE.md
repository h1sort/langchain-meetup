Agents become harder to build not because they get “smarter,” but because we give them more authority.

That gives you a clean taxonomy: agents that know, agents that do, and agents that decide. Your three examples then become an escalating journey from simple architecture to explicit orchestration.

There’s also a useful LangChain nuance for this audience: today, LangChain’s create_agent itself runs on a LangGraph-based runtime. So the interesting choice isn’t really “LangChain or LangGraph”; it’s when the high-level agent abstraction is enough, versus when you need to model the control flow explicitly as a graph. 

Fantastic Agents and Where to Find Them
Subtitle: A field guide to agents, their natural habitats, and how to keep them in their cages.

I’d organize the whole 30 minutes around this visual:

Species

Employee assistant

Client assistant

Fraud specialist

Superpower

Know

Do

Decide

Archetype

Librarian

Concierge

Investigator

Primary need

Information

Tools/actions

Orchestration

Main danger

Wrong answer

Wrong action

Wrong decision

Typical cage

RAG + grounding

Tools + guardrails

Explicit graph + state + HITL

Complexity

Low

Medium

High

Default abstraction

RAG / simple LangChain agent

LangChain agent + middleware

LangGraph

That taxonomy is the thing I’d want people photographing.

Act I — “What kind of creature are we dealing with?” — 4 min
Start with something slightly provocative:

“Not everything with an LLM and a tool needs to become a multi-agent graph.”

Then establish the field guide.

You can define an agent very loosely as:

Model + context + capabilities + some freedom to choose what happens next.

But the important dimension is how consequential that freedom is.

Species 1: Agents that KNOW
They need to find, synthesize and explain information.

“What is our parental leave policy?”

Species 2: Agents that DO
They interact with systems on somebody’s behalf.

“How do I freeze my card?”
→ explain it
→ navigate the app
→ perhaps eventually execute the action.

Species 3: Agents that DECIDE
They collect evidence and contribute to consequential judgments.

“Does this transaction look fraudulent?”

Then introduce the thesis:

More agency → more possible failure modes → stronger cages.

Visually, I’d make it an evolutionary ladder:

KNOW                    DO                       DECIDE

 Information             Actions                  Judgement
     │                       │                         │
     ▼                       ▼                         ▼
   RAG              Agent + tools              Stateful graph
     │                       │                         │
low authority ─────────────────────────────────► high authority
Now the rest of the talk is just visiting three habitats.

Act II — Creature #1: The Employee Assistant — “The Librarian” — 6 min
The job
“Find our internal policy for X.”

“Where is the documentation for Y?”

“How do I request Z?”

At first people instinctively say:

AGENT!

Then reveal:

Maybe you don’t need much of an agent at all.

Architecture:

Question
   ↓
Retrieve internal knowledge
   ↓
Relevant chunks
   ↓
LLM
   ↓
Grounded answer + sources
This is your opportunity to teach the simplest sufficient architecture.

LangChain’s own retrieval documentation distinguishes ordinary RAG from more agentic RAG, where the model dynamically decides when/how to retrieve; agentic retrieval buys flexibility at the cost of more variable behavior and latency. 

Monsters that escape
Now introduce your recurring section:

🐉 Trouble in the cage
For this one:

hallucinated policies
stale documents
bad retrieval
conflicting sources
answering questions the employee isn’t authorized to access
“I couldn’t find it” becoming “I’ll make something plausible up”
The cage
Mostly boring things — deliberately.

Good retrieval
+ permissions
+ metadata / freshness
+ citations
+ grounding
+ fallback when evidence is insufficient
Your lesson:

Don’t solve an information architecture problem with agent orchestration.

If your deterministic RAG pipeline solves the task, you’re done.

That immediately establishes credibility with a LangGraph audience because you’re explicitly not graphing everything.

Act III — Creature #2: The Retail Client Assistant — “The Concierge” — 7 min
Now evolve the librarian.

The customer asks:

“My card isn’t working.”

This isn’t one lookup anymore.

The assistant may need to:

Understand problem
      ↓
Check card status ────────┐
      ↓                   │
Check known incidents     │
      ↓                   │
Search help docs          │
      ↓                   │
Explain / recommend       │
      ↓                   │
Potentially take action ◄─┘
This creature has hands.

What’s different?
The system has tools:

search product/app documentation
retrieve customer/account state
inspect product status
navigate the user to a feature
maybe initiate safe actions
A straightforward LangChain agent is a good fit when you want the LLM to choose among tools dynamically; the current agent abstraction supports sequential/parallel tool calling, retries, error handling and state across tool calls. 

Now the failure modes get much more interesting.

🐉 Trouble in the cage
Wrong tool

It checks the wrong product.

Wrong arguments

Right tool, wrong card/account.

Infinite wandering

Search → search → search → search.

Prompt injection

A retrieved document tries to tell the agent what to do.

Unauthorized action

The model moves from “tell the user how” to “do it.”

Hidden side effects

A tool that looks like retrieval actually changes something.

This gives you a useful distinction:

For a KNOW agent, a mistake is usually a bad sentence.
For a DO agent, a mistake can change the world.

The cage gets stronger
                   ┌── Search docs
                   │
User → Agent ──────┼── Account status
                   │
                   └── Product/API tools
                          │
                    policy / validation
                          │
                     side effects
                          │
                    approval if needed
Talk about:

Tools should be narrow.
Prefer freeze_card(card_id) over execute_banking_operation(...).

Structured interfaces.
Don’t make the LLM produce pseudo-API calls.

Policy around tool execution.

Retries and graceful tool failure.

Human/user confirmation around consequential side effects.

This maps nicely onto LangChain middleware: the framework currently exposes hooks for guardrails, retries, tool-call limits, filtering, logging and human-in-the-loop policies around tool calls. 

And you can foreshadow LangGraph:

“So far our agent chooses what to do. But what happens when we need to control what must happen?”

Act IV — Creature #3: Fraud Agent — “The Investigator” — 8 min
This is the payoff.

A fraud system shouldn’t just be:

transaction → prompt → FRAUD / NOT FRAUD
Put that slide up almost as a joke.

Then:

Our investigator has to construct a case.

For example:

                   ┌→ transaction history ──┐
                   │                        │
Transaction → Triage → device evidence ────┼→ Evidence
                   │                        │
                   ├→ merchant signals ─────┤
                   │                        │
                   └→ known fraud patterns ─┘
                                             ↓
                                      assess / reconcile
                                             ↓
                               ┌─────────────┴─────────────┐
                               ↓                           ↓
                         low confidence              high confidence
                               ↓                           ↓
                         human review                  recommendation
                               │
                               └──────────→ outcome
                                              ↓
                                        audit trail
And now ask:

Should an LLM be allowed to decide which of these steps exist?

Often, no.

Some things are mandatory.

Some happen in parallel.

Some require deterministic branching.

Some need retry/recovery.

Some need human intervention.

Some need durable state.

Now you’ve arrived at LangGraph because the problem demanded it, rather than because your meetup demanded it.

LangGraph’s docs distinguish workflows with predetermined code paths from agents that dynamically choose their own process. Its runtime specifically targets stateful, long-running orchestration with persistence and human-in-the-loop execution. 

🐉 Trouble in the cage
For the specialist:

incomplete evidence
conflicting evidence
false confidence
false positives
steps happening in the wrong order
transient system failures
long-running investigations
needing human escalation
resuming after interruption
auditability
“why did we make this recommendation?”
The cage
Now your cage genuinely looks like a graph.

Explicit states.

Explicit transitions.

Deterministic nodes where appropriate.

Agentic nodes where useful.

Persistent state.

Interrupts.

Human review.

Observable execution.

LangGraph checkpointing is particularly relevant to your story: checkpoints allow execution to pause and resume, support fault recovery, preserve conversational/workflow state and make human-in-the-loop interruption possible. 

A line I’d emphasize:

LangGraph isn’t useful because the fraud agent is “more intelligent.” It’s useful because the workflow has more invariants.

That’s one of the strongest ideas in the talk.

Act V — The Field Guide — 4 min
Now zoom out from the three examples.

Put all three architectures beside each other.

1. Librarian
User → Retrieve → Generate
Freedom: what to say.

2. Concierge
              ┌→ Tool
User → Agent ─┼→ Tool → Agent → Answer
              └→ Tool
Freedom: what to say + which capability to use.

3. Investigator
           ┌→ node ─┐
Input → node         ├→ node → decision
           └→ node ─┘
                ↑
           human / state
Freedom: local reasoning inside a globally controlled process.

Then give them the decision rule.

When should I use what?
Don’t phrase this as:

“When should I use LangChain vs LangGraph?”

Instead:

How much of the control flow am I willing to delegate to the model?

Mostly deterministic information flow
Use normal code / RAG.

Model needs to dynamically select tools
Use a high-level LangChain agent.

You need to control the process itself
Reach for explicit LangGraph orchestration.

Signals you crossed that line:

“This step must happen.”
“These checks should happen in parallel.”
“If X occurs, always go to Y.”
“We need to pause and resume.”
“A human must approve this.”
“The process may last minutes/hours/days.”
“We must know exactly where it failed.”
“I need to replay or inspect state.”
One technical footnote worth saying aloud:

“And these aren’t competing stacks. LangChain’s high-level agent is already backed by LangGraph. I’m really deciding whether I want the prebuilt agent loop or whether I need to own the graph.” 

That will resonate particularly well at this meetup.

The final 60 seconds
Return to the Fantastic Beasts theme.

Put the three creatures back up:

             THE FIELD GUIDE TO AGENTS

       KNOW             DO              DECIDE
        │                │                 │
    Librarian        Concierge        Investigator
        │                │                 │
       RAG           Tool agent       Explicit graph
        │                │                 │
   Ground it        Guard tools       Control flow
And end with something like:

The question isn’t “How do I build an agent?”

It’s:

What freedom does this agent actually need?

Give it that freedom.

And cage everything else.

That, to me, is your talk.

30-minute pacing
I’d allocate 3 min to the premise/taxonomy, 5 min to the employee assistant, 7 min to the retail assistant, 8 min to fraud, 5 min to the architecture/decision framework, and 2 min to the conclusion.

The important thing is that the technical sophistication visibly increases on every example:

RAG → agent loop + tools → graph orchestration.

At the same time, the consequences increase:

wrong information → wrong action → wrong decision.

That gives the talk both a technical arc and a narrative arc, rather than feeling like three unrelated demos.