# Quiz Practice

A single-file, build-step-free slideshow quiz. Everything — markup, styling, and
logic — lives in `index.html`. You load a questions JSON file from your machine; the
app shuffles the questions and runs a one-question-per-slide quiz with scoring and
explanations. All quiz content lives in the JSON — the frontend only renders fields
and compares your click to the stored answer.

## Run

Just **double-click `index.html`** (or open it in any browser). No server, no Node,
no `npm install`. It works straight from a `file://` URL because it reads the JSON
through the file picker instead of `fetch`.

On the start screen you can:

- **Choose JSON file…** — pick a questions file from disk.
- **drag & drop** the file onto the card.
- **Use sample questions** — a few built-in dummy questions to try it out.

The included `questions/arfv.json` (110 questions) and `questions/questions.json`
(small sample) are both ready to load. Use **⤓ Load file** in the header to switch
files mid-session.

## Quiz content (JSON contract)

```json
{
  "meta": { "title": "CTL/LTL Automata Practice", "count": 50 },
  "questions": [
    {
      "id": "q001",
      "type": "ctl_model",
      "prompt": "Is  M ⊨ AGAF¬p  true or false?",
      "diagramSvg": "<svg ...>...</svg>",
      "options": ["True", "False"],
      "correctIndex": 1,
      "explanation": "False. Counterexample: ..."
    }
  ]
}
```

Field rules:

- **`prompt`** — plain string, rendered as monospace text (no parsing). May contain
  logic symbols (`¬ ∧ ∨ → ⊤ ⊥ ◯`) and operator letters (`A E G F X U`).
- **`diagramSvg`** — a raw inline SVG string, or `null` for no diagram. Injected as-is
  (trusted content).
- **`options`** — array of 2–4 strings; one button per option.
- **`correctIndex`** — integer index into `options`.
- **`explanation`** — shown after answering.
- **`id`, `type`** — optional metadata; `type` is shown as a small label.

The app validates each question and skips malformed ones. If the file is unparseable
or has no valid questions, it shows a friendly error on the start screen instead of a
blank page.

## Behavior

- On load, the questions are shuffled; the first 50 are used (fewer is fine).
- Answer by clicking an option → buttons lock, your pick is marked ✓ green or ✗ red
  (with the correct one highlighted green), and a Solution panel reveals the correct
  option plus the explanation.
- Navigate with **Previous / Next** or the **← / →** arrow keys. Going back shows your
  earlier pick and its revealed solution; questions are never re-scored.
- A running **Score** tracks correct answers, and **Restart / new 50** reshuffles and
  resets.
