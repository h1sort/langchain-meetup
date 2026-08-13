# Repository instructions

## Project purpose

This repository contains a 30-minute LangChain/LangGraph meetup talk titled **Fantastic Agents and Where to Find Them**. The talk is an illustrated field guide to agents in production, organized around three fictional specimens:

- **Sourcehound** / *Archivora citans* — KNOW: a bounded retrieval agent with read-only tools.
- **Keypaw** / *Instrumentrix cauta* — DO: a tool-calling agent whose capabilities can create side effects.
- **Caseweaver** / *Inquisitor probabilis* — DECIDE: an evidence agent operating inside an explicit stateful workflow.

The governing thesis is that agent systems become harder to operate as we give them more authority. Production controls therefore escalate from grounding and access, to governed tool execution, to explicit workflow invariants, persistence, and human review.

Use Simon Willison's definition consistently:

> An LLM agent runs tools in a loop to achieve a goal.

All three specimens must satisfy this definition. Deterministic RAG may appear briefly as a baseline, but it is not one of the three specimens.

## Confidentiality and claims

- All companies, users, policies, transactions, architectures, incidents, metrics, and data shown in the talk are fictional or synthetic composites.
- Never imply that an example depicts the speaker's employer or any real internal implementation.
- Say **“a representative production architecture”**, not **“our architecture.”**
- Do not introduce employer-specific branding, screenshots, terminology, vendors, schemas, topology, metrics, or anecdotes.
- Keep the content concrete enough to teach production engineering without suggesting privileged disclosure.

## Reference-only artifact

`masterclass-ai-tools.html` is committed **only as a quality and format reference** for the expected deliverable.

- Do not edit, extend, rename, import, execute as a build input, or use it as the presentation source.
- Do not copy its wording, slide content, branding, proprietary fonts, Santander assets, NOVA/supernova motif, color system, animation code, or DOM/CSS implementation.
- It may be inspected to understand the expected level of polish: full-viewport composition, responsive typography, reusable slide families, restrained reveals, navigation, and presentation ergonomics.
- The actual deliverable is `fantastic-agents.html`, which must remain visually and editorially original.

## Working files

- `REFERENCE.md` — early narrative research and source material; not final copy.
- `SLIDES.md` — detailed production brief and slide requirements.
- `STORY_DRAFT.md` — lean projected copy and speaker beats.
- `VISUAL_SYSTEM.md` — original visual and interaction specification.
- `fantastic-agents.html` — current standalone presentation deliverable.
- `masterclass-ai-tools.html` — immutable reference-only artifact as described above.

When these disagree, preserve the current core thesis and production framing, then reconcile the documents instead of silently allowing them to drift.

## Story and tone

- The fantastic-creature conceit is a memory device, not a franchise imitation or a children's theme.
- Keep the tone mature, technically credible, and lightly playful.
- Use original creature names and artwork. Do not reproduce Harry Potter/Fantastic Beasts creatures, symbols, typography, settings, or other recognizable IP.
- Prefer **handling protocol**, **habitat**, **instinct**, and **tracks** over “cage.” Translate every metaphor into its engineering meaning nearby.
- One visual claim per slide; projected text should be concise enough to understand at a glance.
- Keep the talk within 30 minutes. Prefer 20–24 slides and rehearse below the hard limit.

## Production-agent content requirements

Every specimen should visibly address production concerns, not just framework syntax:

1. authorization and trusted identity;
2. bounded execution, timeouts, and stopping conditions;
3. observability, citations, traces, or audit events;
4. recoverability, persistence, or safe failure behavior as appropriate;
5. evaluation and human oversight proportional to authority.

Distinguish model proposals from application execution. Prompts and tool descriptions guide model behavior; deterministic code and downstream services enforce permissions and invariants.

## HTML presentation requirements

- Keep `fantastic-agents.html` standalone and usable offline unless the project deliberately adopts a documented asset pipeline later.
- Preserve full-viewport slides, keyboard navigation, wheel/touch navigation, progress indication, deep-link review via `?slide=N`, and `prefers-reduced-motion` support.
- Avoid missing local assets and external runtime dependencies.
- Creature and architecture placeholders are acceptable during iteration, but they must look intentional and retain the final layout's footprint.
- Maintain strong projector contrast and responsive layouts. Technical slides must fit within one viewport in presenting mode.
- Code snippets must be concise, legible, and verified against the repository's eventual pinned LangChain/LangGraph versions before delivery.

## Validation

For meaningful HTML changes:

1. run `git diff --check`;
2. extract the embedded script and run `node --check -`;
3. count slides and verify folios remain consistent;
4. render representative story, specimen, architecture, code, matrix, decision, and closing slides in a local browser at 1440×900 or 1920×1080;
5. inspect at least one narrow/mobile viewport when layout code changes;
6. confirm no content is clipped or creates an accidental inner scrollbar.

Use installed Google Chrome in headless mode for local rendering when available. Do not commit temporary screenshots or render directories unless explicitly requested.
