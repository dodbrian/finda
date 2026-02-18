# Finda – Agent Guide

## Project Overview

**Finda** is an architecture documentation project for a personal bank account data pipeline. The documentation is written in [LikeC4](https://likec4.dev/) DSL and captures how bank statements are ingested, parsed, classified, stored, and analysed.

There is **no application source code** in this repository yet — `src/` is reserved for future program source. All meaningful content lives under `docs/architecture/`.

---

## Repository Layout

```
finda/
├── docs/
│   └── architecture/
│       ├── specification.c4   # Element kind definitions and default styles
│       ├── model.c4           # All system elements and relationships
│       └── views.c4           # All rendered diagram views
├── src/                       # Reserved for future application source code
├── implementation_plan.md     # Detailed design narrative and implementation notes
├── package.json               # LikeC4 CLI dev-dependency and npm scripts
└── AGENTS.md                  # This file
```

---

## Technology

| Tool | Purpose |
|------|---------|
| [LikeC4](https://likec4.dev/) `^1.49.0` | Architecture-as-code DSL and diagram renderer |
| Node / npm | Toolchain host for the LikeC4 CLI |

No other runtime dependencies exist.

---

## NPM Scripts

| Script | Command | Purpose |
|--------|---------|---------|
| `dev` | `likec4 start docs/architecture/` | Live-reload diagram server (localhost) |
| `build` | `likec4 build docs/architecture/ -o dist/` | Export static diagram site to `dist/` |
| `preview` | `likec4 preview docs/architecture/` | Preview built output |
| `validate` | `likec4 validate docs/architecture/` | Lint all `.c4` files for DSL errors |

Run `npm install` once to install the LikeC4 CLI before using the scripts above.

---

## Architecture Model Summary

The model describes **Finda** — a personal finance intelligence system — at three levels:

### Actors & External Systems
- **User** (`person`) — the person who owns the bank accounts
- **Bank Statement Source** (`external`) — upstream bank portals / local exports (PDF, CSV, image, text)

### Finda Internal Services (`system` → `service` / `store`)
| Element ID | Kind | Responsibility |
|---|---|---|
| `finda.webApp` | service | Browser-based upload UI and analytics dashboard |
| `finda.ingestionService` | service | Accepts files, detects format, routes to parser or OCR |
| `finda.ocrEngine` | service | Converts image statements to raw text |
| `finda.parserService` | service | Extracts structured transaction records from text/CSV/PDF |
| `finda.classificationEngine` | service | Assigns income/expense categories to transactions |
| `finda.storageService` | service | Persists transactions; query API for other services |
| `finda.transactionDb` | store | Unified relational store for all transaction records |
| `finda.categoryDb` | store | Category taxonomy, classification rules, ML metadata |
| `finda.analyticsService` | service | Aggregates data into reports and spending summaries |

### Key Data Flows
1. **Ingestion & Parsing** — User or bank source → Web App → Ingestion Service → (OCR Engine →) Parser Service
2. **Classification & Storage** — Parser Service → Classification Engine → Storage Service → Transaction DB
3. **Analytics** — Web App → Analytics Service → Storage Service → Transaction DB

---

## Diagram Views

| View ID | Title | Focus |
|---|---|---|
| `index` | Finda – System Landscape | System in context with users and external sources |
| `finda_containers` | Finda – Containers | All internal services and stores |
| `ingestion_flow` | Finda – Ingestion & Parsing Flow | Raw file → structured transaction records |
| `classification_flow` | Finda – Classification & Storage Flow | Categorisation, persistence, and analytics |

---

## Editing Guidelines for AI Agents

1. **Validate after every change.** Run `npm run validate` before committing. The LikeC4 DSL linter will catch unknown element IDs, bad syntax, and broken references.

2. **File responsibilities are strict.**
   - Add/change *element kinds and styles* → `specification.c4`
   - Add/change *elements or relationships* → `model.c4`
   - Add/change *diagram views* → `views.c4`

3. **Element IDs are dot-namespaced.** Children of `finda` are referenced as `finda.<childId>` (e.g. `finda.webApp`). Always use the fully qualified ID in views and relationships defined outside the parent block.

4. **Relationships use `->`.** Syntax: `<source> -> <target> '<label>'`. Labels are optional but recommended for clarity.

5. **Do not introduce npm dependencies** unless absolutely necessary. LikeC4 CLI is the only required tool.

6. **`src/` is intentionally empty.** Do not place architecture documentation files there; use `docs/architecture/` exclusively.

7. **`implementation_plan.md`** is the authoritative design narrative. Consult it when the intent behind a modelling decision is unclear.

---

## Commit Rules

1. **Always validate before committing.** Run `npm run validate` and confirm it exits with no errors. Never commit files that fail DSL validation.

2. **One concern per commit.** Keep commits focused — a single logical change (e.g. adding a new element, adding a view, fixing a relationship). Do not bundle unrelated changes.

3. **Use Conventional Commits format.**
   ```
   <type>(<scope>): <short summary>
   ```
   | Type | When to use |
   |------|-------------|
   | `feat` | Adding a new element, relationship, or view |
   | `fix` | Correcting a broken reference, wrong relationship, or DSL syntax error |
   | `refactor` | Restructuring model or views without changing meaning |
   | `docs` | Updating prose documentation (AGENTS.md, implementation_plan.md, comments) |
   | `chore` | Tooling, package.json, or config changes |

   **Scope** should be one of: `model`, `views`, `spec`, `docs`, `deps`.

   Examples:
   ```
   feat(model): add notificationService element to finda system
   fix(views): correct broken element reference in ingestion_flow
   docs(docs): update AGENTS.md with commit rules
   ```

4. **Subject line rules.**
   - Use the imperative mood: *"add"*, *"fix"*, *"remove"* — not *"added"* or *"fixes"*.
   - Keep it under 72 characters.
   - Do not end with a period.

5. **No generated or build artefacts.** Do not commit the `dist/` directory or `node_modules/`. Both are covered by `.gitignore`.
