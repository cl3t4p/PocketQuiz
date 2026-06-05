# PocketQuiz

A single-file, build-free slideshow quiz that runs straight from your browser. The
entire app — markup, styling, and logic — lives in `index.html`. You load a questions
file from disk, and PocketQuiz shuffles them and runs a one-question-per-slide quiz
with scoring, diagrams, and explanations.

Logic-heavy prompts (CTL / LTL / Büchi automata) are typeset with **MathJax**, so a
prompt written as `M ⊨ AG AF ¬p` renders as proper mathematical notation.

## Running it

**Double-click `index.html`** — or open it in any browser. No server, no Node, no
`npm install`. It works from a `file://` URL because it reads the JSON through the
file picker instead of `fetch`.

On the start screen you can:

- **Choose JSON file…** — pick a questions file from disk.
- **Drag & drop** a file anywhere onto the card.
- **Use sample questions** — a few built-in questions to try things out.

The repo ships two ready-to-load files:

- `questions/arfv.json` — 110 questions (Formal Methods practice).
- `questions/questions.json` — a small 4-question sample.

Use **⤓ Load file** in the header to switch files mid-session.

### MathJax & offline use

Math is typeset by MathJax, loaded from a CDN — so typesetting needs an internet
connection. If you open the app offline, nothing breaks: prompts gracefully fall back
to plain monospace text. The quiz itself (loading, scoring, navigation) is fully
offline either way.

## Question file format

A questions file is a JSON object with a `meta` block and a `questions` array:

```json
{
  "meta": { "title": "CTL/LTL Automata Practice", "count": 50 },
  "questions": [
    {
      "id": "q001",
      "type": "ctl_model",
      "prompt": "Is  M ⊨ AG AF ¬p  true or false?",
      "diagramSvg": "<svg ...>...</svg>",
      "options": ["True", "False"],
      "correctIndex": 1,
      "explanation": "False. Counterexample: ..."
    }
  ]
}
```

### Field reference

| Field          | Required | Description |
|----------------|----------|-------------|
| `prompt`       | yes      | The question text. Logic notation is auto-typeset (see below). |
| `options`      | yes      | Array of 2–4 strings; one button per option. |
| `correctIndex` | yes      | Integer index into `options` of the correct answer. |
| `explanation`  | no       | Shown in the Solution panel after answering. |
| `diagramSvg`   | no       | Raw inline SVG string (injected as-is, trusted), or `null`. |
| `id`           | no       | Identifier metadata. |
| `type`         | no       | Shown as a small label above the prompt. |

Questions missing a valid `prompt`, `options` (2–4 strings), or in-range
`correctIndex` are skipped. If a file is unparseable or has no valid questions, the
start screen shows a friendly error instead of a blank page.

### How prompts are typeset

Prompts are authored in plain Unicode logic notation; PocketQuiz converts the formula
parts to LaTeX at render time and lets MathJax typeset them. **The JSON is never
modified** — this is rendering-only, and any formula it doesn't recognise just stays
as plain text.

Formulas are detected when they're set off from prose by **2+ spaces or a line break**.
The conversion:

- Unicode symbols → math equivalents: `⊨ ¬ ∧ ∨ → ↔ ≡ ⊤ ⊥ ◯ ∀ ∃ ∪ ∩ ⊆ ω φ ψ` …
- Temporal / path operators (`A E G F X U R W`, and runs like `AG`, `AGAF`, `EFEG`)
  render **upright**.
- States like `s0`, `q2` become subscripted (`s₀`, `q₂`).
- Labelled transitions `s0 →{p} s1` become arrows with the label above them.
- Prose is left untouched.

So this prompt:

```
Consider the Kripke model M below.
Is   M ⊨ E(¬p U q)   true or false?
```

renders with `M ⊨ E(¬p U q)` as typeset logic, while the surrounding sentence stays
as ordinary text.

## Behavior

- On load, questions are shuffled and the first 50 are used (fewer is fine).
- Click an option to answer → buttons lock, your pick is marked ✓ green or ✗ red (with
  the correct option highlighted), and a Solution panel reveals the answer and
  explanation.
- Navigate with **Previous / Next** or the **← / →** arrow keys. Going back shows your
  earlier pick and its revealed solution; answers are never re-scored.
- A running **Score** tracks correct answers; **Restart / new 50** reshuffles and
  resets.
