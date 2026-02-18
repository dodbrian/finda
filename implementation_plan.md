# Implementation Plan

[Overview]
Create a LikeC4 architecture documentation draft for "Finda" — a personal bank account data pipeline that parses, classifies, stores, and processes bank statements from text and image sources.

The project "Finda" is a personal finance intelligence system. It ingests bank statements in multiple formats (plain text, CSV, PDF, images), extracts transaction records through parsing and OCR, classifies each transaction into income/expense categories, persists everything into a unified database, and exposes processed data for further reporting and analysis. 

This LikeC4 documentation captures the system at three levels of detail: (1) a system landscape overview showing Finda in context with its users and external sources, (2) a container-level breakdown of the major processing components, and (3) focused views on the two core data flows — ingestion/parsing and classification/storage. The diagrams use the C4 model conventions expressed in LikeC4 DSL (.c4 files).

[Types]
Define element kinds for actors, systems, services, stores, and external integrations.

LikeC4 element specifications used:
- `person` — human users interacting with the system (shape: person)
- `system` — top-level bounded systems (shape: rectangle)
- `service` — internal microservice or processing unit (shape: rectangle)
- `store` — data persistence component (shape: storage)
- `queue` — asynchronous messaging component (shape: queue)
- `external` — third-party or out-of-scope system (shape: browser / rectangle, color: muted)

[Files]
Three .c4 source files plus one top-level specification file are to be created under `docs/architecture/`.

New files to be created:
- `docs/architecture/specification.c4` — global element kind definitions and shared styles
- `docs/architecture/model.c4` — complete system model: all elements and relationships
- `docs/architecture/views.c4` — all diagram views (landscape, containers, ingestion flow, classification flow)
- `docs/architecture/globals.c4` — optional: shared color palette / theme constants (can be merged into specification.c4)

Note: `src/` is reserved for program source code.

[Functions]
No functions — this is a documentation-only project using declarative DSL.

There are no functions, methods, or executable code in this plan. All LikeC4 constructs are declarative specifications.

[Classes]
No classes — all constructs are LikeC4 model elements and views.

LikeC4 model elements (analogous to "classes" in the DSL):
- `user` (person) — the person who owns the bank accounts
- `finda` (system) — the top-level Finda system boundary
  - `finda.ingestionService` (service) — accepts uploaded statements, coordinates parsing
  - `finda.ocrEngine` (service) — converts image statements to raw text (OCR)
  - `finda.parserService` (service) — extracts structured transaction records from text
  - `finda.classificationEngine` (service) — assigns category tags to each transaction
  - `finda.storageService` (service) — persists and retrieves data
  - `finda.analyticsService` (service) — aggregates and processes stored transactions
  - `finda.webApp` (service) — user-facing dashboard / upload UI
  - `finda.transactionDb` (store) — unified transaction database
  - `finda.categoryDb` (store) — category/taxonomy reference store
- `bankSource` (external) — upstream bank portals / statement exports (PDF, CSV, image)

[Dependencies]
No new npm dependencies needed beyond the already-installed `likec4` package.

`likec4@^1.49.0` is already installed as a dev dependency. No additional packages required. The `npm run dev` script (`likec4 start docs/architecture/`) will serve the diagrams locally. Validate with `npm run validate`.

[Testing]
Validation is done via the LikeC4 CLI linter.

- Run `npm run validate` to check DSL syntax across all `.c4` files in `docs/architecture/`
- Run `npm run dev` to serve and visually inspect all diagrams in a browser
- Manually review each view for correctness: landscape, containers, ingestion flow, classification flow
- Confirm all referenced element IDs exist (no broken references)
- Confirm all relationships are bidirectionally sensible

[Implementation Order]
Create files in dependency order: specification first, then model, then views.

1. Create `docs/architecture/specification.c4` — defines element kinds and default styles
2. Create `docs/architecture/model.c4` — defines all elements and their relationships (depends on specification)
3. Create `docs/architecture/views.c4` — defines all diagram views (depends on model)
4. Run `npm run validate` to confirm DSL is valid
5. Run `npm run dev` to visually review all diagrams
