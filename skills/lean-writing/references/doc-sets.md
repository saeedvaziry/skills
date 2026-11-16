# Doc sets

For a folder of docs that describes a product or system and is kept up to date.

## Layout

```
docs/
  product/     what it does and how it behaves
    README.md  one overview diagram, how it works in a few steps, index table
    01-concepts.md
    02-...
    NN-examples.md
  tech/        how it is built
    README.md
    01-...
```

- One topic per file, about 100 lines at most. A longer file is two topics.
- Number files in reading order. Examples go last.
- Every file is listed in its folder's `README.md`, in a table of "Doc" and "Covers".
- Keep "what it does" and "how it is built" in separate folders. The first has no tech stack, APIs or schemas.
- Temporary material (plans, task lists, handoff notes) stays out of both, and out of `AGENTS.md`. It lives in its own folder and is deleted when the work is done.

## Glossary

- One file defines every term in one line each, next to a diagram of how the things relate.
- A new concept gets its glossary line before it is used anywhere else.
- Use the glossary's word exactly. Do not vary it for style.

## Keeping files in step

A fact usually appears in several places: a diagram, a table, an example. Write down which, once, so that a change reaches all of them.

| If you add | Also update |
|---|---|
| A concept | The relationship diagram and the glossary |
| Something a user or agent can do | The table of who can do what |
| A way things go wrong | The table of failures and what happens next |
| A new file, or a change in scope | The folder's `README.md` |

Build this table for the project and keep it in `AGENTS.md`.

## Adding or changing something

1. Find the gaps. Ask the user about the ones that change the shape of the product, with concrete options. Settle the rest with the simplest sensible answer.
2. Write or extend one doc.
3. Update everything it touches, using the table above.
4. Tell the user which gaps you settled yourself. Say it in the reply, not in the docs.

## Facts not yet confirmed

A doc states facts. When a fact about an outside tool is not confirmed, do not blur it into the text. Put it in a table at the end of the file:

| Question | Why it matters |
|---|---|
| Does the CLI report a usage limit in its JSON output? | The run must end as blocked with that reason |

Delete the row when the answer is known and written into the doc.

## Section for the project's AGENTS.md

Paste this, then replace the bracketed parts.

```markdown
## Writing docs

Fewest words that leave nothing unclear. Diagrams and tables carry the content; prose connects them.

| Use | For |
|---|---|
| State diagram | Anything with a lifecycle |
| Sequence diagram | Who talks to whom, in order |
| Flowchart | Decisions and overviews |
| Entity diagram | How things relate and how many |
| Table | Fields, options, who can do what, cause and effect |
| Bullets | Rules, one line each |
| Sentence | One line to introduce a section or diagram |

- State how it works, in present tense. No rationale, alternatives, decision logs, open questions or history.
- One or two diagrams per section. If a table says it, skip the diagram.
- One idea per bullet. No paragraph longer than three lines.
- Use concrete examples from this project, not abstract ones.
- Use the glossary terms in [path to glossary] exactly. One name per thing.

## Files

- `docs/<folder>/NN-topic.md`, numbered in reading order. One topic per file, about 100 lines at most.
- Every file is listed in its folder's `README.md`.

## Adding or changing a feature

1. Ask the user about gaps that change the product's shape, with concrete options. Settle the rest simply.
2. Write or extend one doc.
3. Update everything it touches:

| If you add | Also update |
|---|---|
| [kind of change] | [files and tables to update] |

4. Tell the user which gaps you settled yourself, in the reply, not in the docs.
```
