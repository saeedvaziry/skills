---
name: lean-writing
description: Write project text that is short, structured and free of filler. Covers docs, READMEs, specs, plans, design notes, AGENTS.md files, reports, summaries, handoffs, PR descriptions, commit messages and replies to the user. Use it for almost any write-up in a project, including when the user only says "document this", "write this up", "summarise", "explain how X works", "update the docs", or asks for a plan or a report, and whenever existing text reads as padded, vague or machine-written and needs tightening. Not for marketing copy, fiction or legal text.
---

# Lean writing

The standard: the fewest words that leave nothing unclear.

The reader arrives cold, is busy, and cannot ask what you meant. Each sentence either tells them something they need or costs them attention. Filler is not harmless: it hides the sentences that matter and teaches the reader to skim.

If the project has its own writing rules, they win. This skill fills the gaps.

## 1. Know the point first

- Say the point to yourself in two sentences before drafting. If you cannot, the problem is the thinking, not the wording. Read more or ask, then write.
- Decide who reads it and what they do next. A page for someone building the thing differs from a note for someone deciding about it.

## 2. Pick the form that carries the content

| Content | Form |
|---|---|
| Something with a lifecycle | State diagram |
| Who talks to whom, in order | Sequence diagram |
| Decisions, an overview | Flowchart |
| How things relate, and how many | Entity diagram |
| Fields, options, who can do what, cause and effect | Table |
| Rules | Bullets, one line each |
| Steps to follow | Numbered list |
| A trade-off, a reason | Short prose |
| One fact | One sentence |

- Prose is for reasoning. Bullets cannot hold a "because", so do not chop an argument into them.
- A diagram earns its place when it shows a relationship a table cannot. If a table says it, skip the diagram. One or two per section.
- A table with one row is a sentence. A page with one point needs no headings.

## 3. Write it plainly

| Rule | Why |
|---|---|
| State how things are, in present tense | The reader wants the system, not its history |
| One idea per bullet | Two ideas in one bullet hide the second |
| No paragraph over three lines | Long blocks get skipped |
| One name per thing, everywhere | A synonym makes the reader think there are two things |
| Examples from this project, not abstract ones | `DEPLOY_TOKEN` teaches more than "a secret" |
| Exact names, numbers, paths and commands | "About an hour" and "the config file" send the reader searching |
| Full sentences | Short is not telegraphic: dropped articles, arrows used as grammar and private abbreviations are clear only to the writer |

## 4. Leave out

| Leave out | Unless |
|---|---|
| Why a choice was made, alternatives considered | It is a decision record, or the reader has to choose |
| What it used to be, what changed | It is a changelog or commit message |
| Open questions | Never in a doc. Ask the person, then write the answer as a fact |
| The process you followed | The reader must repeat it |
| An opening that announces the document | |
| A closing that repeats it | |
| The same fact twice | |
| Hedges on things you know | The doubt is real: then state it once, exactly, where it applies |
| Intensifiers, praise, sales adjectives | |
| Offers of more help, sign-offs | |

| Instead of | Write |
|---|---|
| "This document provides a comprehensive overview of the sign-in system." | Nothing. Start with the first fact. |
| "It's important to note that tokens expire after one hour." | "Tokens expire after one hour." |
| "The system leverages a robust, scalable queue to seamlessly process jobs." | "Jobs go through a queue." |
| "After evaluating Postgres and MySQL, we decided on SQLite because..." | "Data is stored in SQLite." |
| "There are several things to keep in mind:" then a list | The list |
| "I hope this helps! Let me know if you'd like me to..." | Nothing |

## 5. Reports, summaries and handoffs

Text that tells someone what you did: a reply after work, a PR description, a status note, a handoff.

- Lead with what the reader most needs: the result, or the decision you need from them.
- Say what you checked and how. Say plainly what you did not check. "Done" is not actionable until the reader knows how sure it is.
- List the choices you made on your own, so they can be corrected.
- Describe the result, not the journey.
- Use the reader's words. A label you coined while working means nothing to them; write the longer thing it stands for.
- Write one message the reader could act on if it were the only one they read today.
- A handoff holds only what the next person cannot get from the docs or the version history.
- A commit or PR title says what changes for someone using the code. The body is for why, and how to verify.

## 6. Ask instead of guessing

- Ask only what changes the shape of the result. Settle the rest with the simplest sensible answer, and report that you did.
- Offer concrete options, each with its consequence in one line, and mark the one you recommend.
- Write the answer into the doc as a fact, not as a question and answer.

## 7. Mermaid diagrams

- Keep a diagram under about 15 lines. Split or cut otherwise.
- Short edge labels. No semicolons.
- Give multi-word states an alias: `state "Human turn" as HumanTurn`.
- Quote multi-word relationship labels: `AGENT }o--|| MACHINE : "runs on"`.
- Do not name a node `end` or `default`.
- Render to check the syntax when a renderer is available. If not, say the diagrams are unrendered.

## 8. Before you send it

1. Read it as someone who was not there. What would they have to ask?
2. Remove each sentence in turn. If nothing is lost, leave it out.
3. Would a table or diagram replace a paragraph? Would a sentence replace a table?
4. Is each thing called by the same name throughout?
5. Is anything said twice?
6. Does the first line carry the point?

## When to write more

- **A newcomer, or a request for depth:** add definitions and the sentences that connect ideas. Still no filler.
- **A decision record or review:** the reasons and alternatives are the content.
- **Warnings about irreversible steps:** keep them in full.

## A folder of docs

When creating or maintaining a set of docs rather than one page, read [references/doc-sets.md](references/doc-sets.md). It covers file layout, the index, the glossary, keeping files in step with each other, and a section to paste into the project's `AGENTS.md` so every agent writes this way.
