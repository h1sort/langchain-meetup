# Fantastic Agents — Visual & Interaction System

**Status:** first-pass design specification. This document defines a visual language for the deck, not implementation code and not final slide copy.

## 1. Design intent

The deck should feel like a mature field guide written by an engineer: part natural-history specimen notebook, part production operations manual.

The creatures create recall. The diagrams, code, and handling protocols earn trust. Neither mode should undermine the other.

The reference deck establishes the level of finish to aim for: intentional full-viewport composition, a small set of reusable layout families, strong opening/closing moments, progressive disclosure, and a responsive presentation surface. It must **not** be treated as a source of branding, artwork, typefaces, wording, motifs, or code. This system uses no Santander colors, NOVA/supernova imagery, proprietary assets, or proprietary fonts.

### Creative rules

- **Field guide, not fantasy franchise.** No aged parchment texture, spellbook typography, crest, wand, school imagery, or visual borrowing from existing fictional creatures.
- **Operational, not sterile.** Diagrams have the hand-drawn clarity of a working architecture review, balanced by crisp typesetting and structured evidence.
- **One visual claim per slide.** A technical slide may be dense only when the density *is* the point (the three habitat diagrams and the final matrix).
- **Every metaphor translates.** “Habitat,” “tracks,” “instinct,” and “handling protocol” always have a nearby engineering interpretation.
- **Authority becomes visible.** KNOW, DO, and DECIDE are not only labels; their respective colors, marks, and boundaries encode the scope of delegated freedom.

### Suggested production format

- Primary canvas: 16:9 at 1920 × 1080 (or an equivalent responsive full-viewport presentation).
- Usable content area: 5.5% left/right and 6% top/bottom safe zone. Keep citations and page markers inside it.
- Default grid: 12 columns, 24 px baseline at 1920 px. Do not force every slide to use all 12 columns.
- Default rhythm: dark specimen/claim slides alternate with bright notebook/technical slides. The contrast signals a change from narrative to evidence.

## 2. Design tokens

### Palette

The base is ink, paper, graphite, and field-note ochre. The three specimen colors are reserved for their specimen and for meaningful architecture semantics—not decoration.

| Token | Hex | Use |
| --- | --- | --- |
| `--ink` | `#101817` | Dark slide background; high-contrast title field |
| `--ink-raised` | `#1B2725` | Dark cards and diagram panels |
| `--paper` | `#F6F3EB` | Default technical-slide background |
| `--paper-warm` | `#ECE7DA` | Cards, table headers, code surroundings |
| `--graphite` | `#25302E` | Primary text on paper; diagram strokes |
| `--muted` | `#66706D` | Supporting labels and metadata |
| `--rule` | `#C9CDC4` | Borders, separators, quiet diagram strokes |
| `--ochre` | `#C99637` | Field-guide annotation, section index, non-alarm emphasis |
| `--source` | `#277C73` | Sourcehound / KNOW / evidence and read-only paths |
| `--key` | `#B95332` | Keypaw / DO / constrained side effects and tool proposals |
| `--case` | `#6559A7` | Caseweaver / DECIDE / state and evaluation |
| `--safe` | `#2C775E` | Approved / read-only / completed states; pair with text/icon |
| `--caution` | `#B77221` | Review or limit required; pair with text/icon |
| `--stop` | `#A93D38` | Rejected / blocked / failed action; use sparingly |

On dark slides, use `#F7F5EF` for primary type and `#CBD1CA` for secondary type. Do not place body copy directly over creature art without an opaque or near-opaque scrim.

### Color semantics

- **Teal/Sourcehound:** evidence enters or is read from the system.
- **Terracotta/Keypaw:** a proposed tool call or real side effect is in view. A write is never represented by color alone; use a filled arrowhead and `WRITE`/`REVIEW` label.
- **Violet/Caseweaver:** persistent case state, evaluation, routing, and synthesis.
- **Graphite:** deterministic application code and neutral infrastructure.
- **Ochre:** editorial annotation, not a risk score. It should never compete with stop/caution states.

### Typography

Use a three-role type system. Load web fonts only if they are available before the talk; the fallback stack must still look deliberate offline.

| Role | Preferred web font | Safe fallback | Use |
| --- | --- | --- | --- |
| Display / specimen name | `Source Serif 4`, 600–700 | Georgia, `Times New Roman`, serif | Titles, creature names, short pull quotes |
| UI / body | `IBM Plex Sans`, 400–700 | Inter, `Segoe UI`, Arial, sans-serif | Body text, labels, tables, diagram nodes |
| Code | `IBM Plex Mono`, 400–500 | `SFMono-Regular`, Consolas, `Liberation Mono`, monospace | Code, function names, structured data |

Rules:

- Species names are italic serif; common names are uppercase sans with generous tracking.
- Use sentence case for slide titles. Reserve all caps for concise labels (`KNOW`, `HABITAT`, `READ ONLY`).
- Do not use a handwritten font. “Hand-drawn” belongs in Excalidraw lines, not in the reading surface.
- Suggested 1920 × 1080 scale: 68–88 px title, 34–44 px section title, 24–30 px body, 18–22 px label, 20–24 px code. Never project body text smaller than 22 px.

### Shape, line, and surface

- Corner radius: 12 px for UI-like cards; 0 px for specimen labels, evidence strips, and code panels. The contrast keeps the deck from becoming a card soup.
- Border: 1–2 px; use `--rule` on light panels and 20–30% white on dark panels.
- Diagram line: 2.5–3 px in Excalidraw, roughness low-to-medium. Keep connector routing clean and mostly orthogonal.
- Texture: optional, extremely low-opacity paper grain (`≤ 3%`) on light slides only. No faux distressing, stains, or scroll edges.
- Shadow: soft and shallow (`0 8px 24px` at low opacity) on paper cards only. No glow as a default visual effect.

## 3. Persistent slide chrome

Every slide gets quiet orientation aids:

- **Top-left field marker:** `FIELD GUIDE / 01` or `SPECIMEN II / DO`; small sans, tracked uppercase.
- **Top-right authority mark:** one of `KNOW`, `DO`, `DECIDE` when inside a specimen section. Use word + shape, not color alone: circle / square / lozenge respectively.
- **Bottom-left source strip:** only when a citation, definition, framework reference, or fictional-data disclaimer matters. Keep to one short line, 70% opacity.
- **Bottom-right folio:** `07 / 24`, small and quiet.

The opening and closing slides may remove all chrome except a minimal folio. Technical diagrams must retain it, so a photographed slide is intelligible out of sequence.

## 4. Slide families

These are reusable templates, not a mandate to make every slide uniform.

| Family | Best for | Composition | Planned slides |
| --- | --- | --- | --- |
| **Field plate** | Opening / final statement | Full-bleed ink field, creature art/silhouettes, one bold statement | 1, 24 |
| **Claim plate** | A single conceptual turn | Dark or paper background; oversized sentence; one small supporting diagram or comparison | 2, 3, 4, 15, 17, 23 |
| **Specimen reveal** | Introduce each creature | 5-column text card + 7-column illustration, with a taxonomic caption rail | 5, 10, 16 |
| **Habitat map** | Production architecture | Paper background, large editable Excalidraw canvas, legend and a small diagram-stage label | 7, 12, 18 |
| **Implementation plate** | Code and the implementation choice | 7-column code panel + 5-column annotations/control card | 8, 13, 19 |
| **Handling sheet** | Failure modes and controls | Notebook table or annotated creature detail; no more than six rows | 9, 14, 20 |
| **Reference matrix** | Compare systems / decision guide | Wide, high-legibility table or path; designed for a phone photograph | 21, 22 |

### Recommended narrative alternation

For each specimen, maintain the visible rhythm already implied in `SLIDES.md`:

1. **Reveal** — what animal is this?
2. **Habitat** — what is around it in production?
3. **Implementation** — what code abstraction matches it?
4. **Handling** — what does it do badly and how do we control it?

This gives the deck a predictable cadence without making its point predictable.

## 5. Beast artwork and interim placeholders

### Final artwork direction

Each animal belongs to the same invented ecosystem. The medium is restrained editorial naturalism: ink contour, limited wash, subtle topographic/evidence marks. It should feel adult and scientific rather than plush, heroic, or cartoonish.

- **Sourcehound:** low, alert silhouette; evidence fragments and tracks; teal micro-details.
- **Keypaw:** upright/dexterous silhouette; distinct instrument-ready forelimbs; terracotta authorization band.
- **Caseweaver:** deliberate, many-sensed silhouette; converging evidence threads; violet state markings.

Use art once at section entry, once in the closing group portrait, and as small cropped headmarks in the final matrix. Do not turn every technical slide into creature wallpaper.

### Art frame specification

- Portrait frame: 4:5 crop; preserve 15% clear negative space on the text-facing side.
- Group silhouette: 16:9 transparent PNG/SVG-compatible asset with the three figures ordered left-to-right by authority.
- Background: transparent or clean `--ink`/`--paper`; never rely on embedded text.
- Caption rail: common name, italic binomial, authority label, and a one-line instinct.
- Asset names when created: `sourcehound-plate.png`, `keypaw-plate.png`, `caseweaver-plate.png`, `three-specimens-silhouette.png`, `three-specimens-hero.png`.

### Placeholder treatment until art exists

Use a deliberate museum-card placeholder, not a broken-image box:

```text
┌──────────────────────────────┐
│  SPECIMEN PLATE / PENDING    │
│                              │
│        [line silhouette]     │
│                              │
│  Sourcehound                 │
│  Archivora citans            │
│  portrait art to be supplied │
└──────────────────────────────┘
```

- Draw the placeholder silhouette from 3–5 abstract strokes; do not imitate a known creature.
- Use the specimen’s accent as one narrow corner rule and the caption’s status dot.
- Keep the same crop and negative space as the final asset, so later asset replacement changes no layout.
- Mark placeholders `ARIA-HIDDEN` if their adjacent caption conveys all semantic information; otherwise supply a descriptive alt text.

## 6. Architecture diagrams and Excalidraw placeholders

### Diagram grammar

The habitat maps should be editable Excalidraw scenes. They should look hand-drawn because they are explanatory, not because they are whimsical.

| Visual | Meaning |
| --- | --- |
| Rounded rectangle | Application/runtime component |
| Cylinder | Durable data, index, checkpoint, audit store |
| Hexagon | Policy, validation, authorization, or required gate |
| Cloud | External API/system |
| Small four-point marker | Model reasoning call |
| Solid arrow | Executed data/control flow |
| Dashed arrow | Proposed call, trace, or asynchronous review |
| Thick enclosing boundary | Trust zone or workflow topology |
| Double outline | Human review / interrupt boundary |

Never use a cloud icon merely because a component runs on cloud infrastructure. It means an external system in the visual language. Keep vendor names and logos out of the main diagrams.

### Color and line rules in diagrams

- Base nodes and arrows are graphite; background is paper.
- Apply the specimen accent only to the **meaningful path**: evidence for Sourcehound, proposed/executed actions for Keypaw, persisted state/route for Caseweaver.
- Side-effecting calls: terracotta label + solid arrow + `WRITE` tag.
- Human review: double boundary + a named `REVIEW` step. Never use only a person icon.
- Trust boundary: graphite/ochre outline with a written label, e.g. `internal data boundary`.
- Include a 3–5 item legend only on the first habitat slide; later slides retain the same grammar and need only a compact key for any new symbol.

### Progressive reveal choreography

Build diagrams in semantic groups; each advance should answer one spoken sentence.

| Habitat | Reveal sequence |
| --- | --- |
| Sourcehound | request/identity → runtime/model loop → read-only tool gateway → ACL/data sources → grounded output → limits/traces/ingestion lane |
| Keypaw | customer/agent → proposed tool call → policy + validation boundary → safe reads → write route → confirmation/audit → traces/limits |
| Caseweaver | event/triage → parallel evidence fan-out → evidence state/join → assess → route → human/recommendation → checkpoints/traces |

Avoid using animation to move boxes around. Fade or draw in the next semantic group while already-visible components stay fixed.

### Interim architecture placeholder

Until an `.excalidraw` scene is produced, reserve the actual diagram frame and render this compact brief inside it:

```text
HABITAT MAP / EDITABLE EXCALIDRAW PENDING

Intent: [one sentence]
Build:  01 request → 02 runtime → 03 policy boundary → 04 outcome
Legend: rounded = service · hexagon = gate · dashed = proposal
Source: fictional representative production architecture
```

The placeholder uses a light dotted grid and three neutral grey blocks, not a pseudo-diagram detailed enough to mislead. It must retain the final diagram’s aspect ratio and caption location.

## 7. Code-slide design

Code must look executable but should be edited for projection. The audience should see the control point in under three seconds.

### Layout

- Code occupies 58–65% of the slide width, aligned to the left on paper or deep ink.
- Right-side control card contains at most four annotations. Each has a short heading and a single sentence.
- Include a 1-line eyebrow: `IMPLEMENTATION / BOUNDED RETRIEVAL LOOP`.
- Use real language syntax colors sparingly: graphite/light text, specimen accent for framework/control calls, ochre for literals/limits, muted comments. Never rainbow-highlight every token.
- Lines not discussed are dimmed to 45–55% opacity; the active 2–5 lines are full opacity.

### Code panel specification

- Deep panel: `--ink` on paper slide, 12 px radius, 1 px `--rule` border; or paper panel on a dark slide.
- 20–24 px monospaced text at 1920 × 1080, line height 1.45–1.55.
- Maximum 8–14 visible lines. If more is necessary, use an ellipsis line and link/QR to the runnable version.
- No line numbers unless the speaker references them. No terminal chrome, fake editor tabs, git status, or distracting window controls.
- Use visible focus blocks/callout leaders—not only a yellow highlighter effect—to identify identity propagation, limits, validation, interrupts, or graph edges.

### Specimen-specific treatment

| Slide | Accent / focal code | Annotation focus |
| --- | --- | --- |
| 8 / Sourcehound | Teal on read-only tool list and run limit | trusted context, ACLs, evidence schema, abstention |
| 13 / Keypaw | Terracotta on interrupt/write tool | narrow interface, server validation, approval, audit |
| 19 / Caseweaver | Violet on graph nodes and conditional edge | topology, deterministic nodes, model node, human node |

## 8. Handling sheets and evidence tables

Handling slides are a key tonal device: they turn a whimsical specimen name into serious production practice.

- Use a clean two-column table: `Observed behavior` / `Handling protocol`.
- Give each row a small semantic marker: track, gate, timer, provenance strip, review stamp. Those marks are labels, not decorative clip art.
- Alternate row fill very lightly (`--paper` / `--paper-warm`) and keep borders quiet.
- One row may receive the specimen accent as the speaker’s focal point; do not color every row.
- In the Caseweaver sequence diagram, show a visible time break (`minutes or days`) as a horizontal gap, then resume state from the checkpoint. This is the strongest visual explanation of persistence.

For the final matrix (slide 21), use portraits as column headers, not as a separate decorative strip. The table needs strong row labels and no tiny prose. If it cannot be read in a phone photograph, it is not ready.

## 9. Interaction and motion system

### Default interaction model

- Keyboard: arrows, space, Page Up/Down, Home/End.
- Click/tap: advance only when not selecting content. Keep a visible but unobtrusive next-slide affordance for web delivery.
- A slim progress rule may appear on desktop, using graphite on light slides and paper at 45% opacity on dark slides. It should not be a branded signature.
- Maintain stable URLs or slide IDs for remote rehearsal and review.

### Motion rules

- Motion follows explanation. Do not autoplay a full diagram, or run ambient animation behind dense content.
- Standard reveal: 220–350 ms opacity + 8–12 px vertical ease-out. Use 80–140 ms staggering only for a short sequence of 3–5 elements.
- Diagram reveals: 250–400 ms per semantic group, triggered by presenter advance. A line may “draw” once; no continuously moving packets.
- Field plate: one restrained 600–900 ms silhouette/label arrival is enough. Prefer an optical shift, paper grain, or subtle focus pull over particles, explosions, glows, or loops.
- Do not animate text character-by-character. It slows reading and feels ornamental.
- No animation should delay the speaker by more than one second after a slide advance.

### Reduced motion

Honor `prefers-reduced-motion: reduce`:

- Render all intended reveal states immediately or on simple fade.
- Remove path drawing, parallax, ambient texture shifts, and all auto-looping animation.
- Preserve diagram build order through manual presenter advances; the static result must remain coherent.

## 10. Accessibility and projection checks

- Minimum text contrast: 4.5:1 for body text; aim for 7:1 in a room with projectors. Verify specimen accent text on both paper and ink before use.
- Color is never the only state cue. Pair it with text, a line style, a symbol, or spatial position.
- All technical visuals have a concise caption/alt description in the presenter/source version. Example: “Policy gate sits between the model-proposed freeze-card call and the action API; confirmation is required before execution.”
- Creature art is decorative when a specimen card is present; empty alt text is appropriate in that case. If it carries meaning, describe the distinguishing feature, not every visual detail.
- Keep citations large enough to read in a recording: 16 px absolute minimum at 1080p, preferably 18 px.
- Test at 1280 × 720, in a dim projector, and from the back of a room. The real constraint is sightline, not a browser’s perfect retina display.
- Avoid relying on hover states, audio cues, cursor location, or color flashing to convey meaning.

## 11. Responsive behavior

The talk is designed for a widescreen stage, but review and rehearsal will happen on smaller screens.

| Viewport / context | Behavior |
| --- | --- |
| 16:9 projector (primary) | Full composition; fixed 16:9 art/diagram frames; no scroll within a slide |
| 4:3 projector | Center-crop background art only; preserve text and diagram safe zone; allow diagram scale-down before text scale-down |
| Tablet / laptop review | Keep slides full-viewport; tighten outer padding; code annotations move beneath code if necessary |
| Mobile review | Single-column stack for non-presenting mode; show diagrams as zoomable image/canvas and retain a text summary; do not pretend the live deck is readable at phone size |
| Short viewport | Reduce decorative artwork first, then vertical gaps, then secondary labels. Never shrink body/code below the projection minimum |

For tables and diagrams, a review mode may permit vertical scroll or zoom. The presenting mode must never introduce accidental scrollbars inside a slide.

## 12. Reusable CSS component inventory

This is an implementation-facing inventory. Names are suggestions, not a requirement to build the deck in HTML.

### Foundation

| Component | Responsibility |
| --- | --- |
| `.deck` | Presentation shell, keyboard focus, slide counter state |
| `.slide` | 16:9/full-viewport layout and safe-area padding |
| `.slide--ink`, `.slide--paper` | Background and default contrast pairing |
| `.slide__chrome` | Field marker, folio, source strip |
| `.eyebrow`, `.authority-mark`, `.species-name` | Reusable editorial labels |
| `.reveal`, `.reveal-group` | Presenter-controlled reveal states and reduced-motion fallback |

### Narrative and specimen

| Component | Responsibility |
| --- | --- |
| `.field-plate` | Full-bleed title/closing composition with controlled art scrim |
| `.claim-plate` | Large statement, brief support, optional micro-diagram |
| `.specimen-card` | Common name, binomial, instinct, authority, habitat |
| `.specimen-art` | Fixed crop, decorative semantics, negative-space positioning |
| `.specimen-placeholder` | Museum-card placeholder for missing art |
| `.accent--source`, `.accent--key`, `.accent--case` | Scoped specimen color tokens; never global page recoloring |

### Technical content

| Component | Responsibility |
| --- | --- |
| `.habitat-map` | Diagram frame, caption, legend, diagram-stage label |
| `.diagram-placeholder` | Honest placeholder for a pending Excalidraw source |
| `.diagram-legend` | Shared shape/line grammar |
| `.code-plate` | Code frame, line emphasis, language label |
| `.control-notes` / `.control-note` | Four-or-fewer code annotations |
| `.handling-sheet` | Failure/control comparison table |
| `.status-mark` | Text + icon/shape state marker (safe, caution, stop) |
| `.decision-path` | Branching decision guide for slide 22 |
| `.field-matrix` | Photographable final comparison table |

### Utilities and constraints

| Component | Responsibility |
| --- | --- |
| `.sr-only` | Screen-reader-only explanatory text |
| `.visually-muted` | De-emphasized context, never essential data |
| `.content-safe` | Applies safe-area max width and padding |
| `.no-motion` | Explicit reduced-motion or static-export state |
| `.print-export` | Stable high-contrast state for PDF/image export |

Avoid a generic `.card` component with endless variants. Components should correspond to a rhetorical job in the talk.

## 13. Acceptance checklist for the first visual pass

- [ ] Slides 1, 5, 10, 16, and 24 communicate even with placeholder art.
- [ ] Each specimen has one identifiable accent, silhouette, and authority mark.
- [ ] Each habitat diagram is framed as a fictional representative production architecture and remains editable in Excalidraw.
- [ ] Every code slide has one focal control and fewer than 14 visible lines.
- [ ] All diagram/protocol symbols are explained at least once and use text in addition to color.
- [ ] There is no visual reference to a specific company, cloud provider, or existing fantasy franchise.
- [ ] The final matrix is readable from a phone photo and the close lands without a dense extra slide.
- [ ] The deck is usable without motion and survives an offline font fallback.
