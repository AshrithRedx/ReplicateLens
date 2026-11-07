# ReplicateLens — Master Project Handoff

This is the consolidated project context for the new ChatGPT Project. The separate files in the ZIP are easier to maintain; this single document is provided for one-file upload convenience.


---

# FILE: README_START_HERE.md

# ReplicateLens — Start Here

**Working title:** ReplicateLens  
**Product:** AI Research Implementation Intelligence & Reproducibility Assessment  
**Target:** A credible, publicly hosted MVP in eight weeks  
**Status:** Project committed; implementation has not started  
**Primary coding agent:** OpenCode  
**Project overseer:** ChatGPT Project

## 1. What we are building

ReplicateLens helps a researcher or engineer answer a practical question:

> “I found an interesting research paper. What is its implementation, what do I need to run it, what evidence exists in the repository, and what information is still missing?”

A user submits an arXiv URL/ID, DOI, or supported open-access paper URL. ReplicateLens resolves the paper, finds possible official code repositories, statically inspects a user-confirmed repository, extracts verifiable implementation evidence, and creates a source-linked implementation-readiness report. The user can ask questions against that paper and repository evidence. A custom MCP server exposes the same read-only research capabilities to compatible AI clients.

**This is an evidence and planning product, not an automatic paper-reproduction engine.** The first release will not run arbitrary repositories or claim that a paper has been reproduced.

## 2. The operating model

- **ChatGPT Project = product owner, architect, scope guardian, reviewer and project memory.** It prepares bounded tasks, checks decisions, reviews diffs and test results, and maintains project state.
- **OpenCode = primary implementation agent.** It explores the codebase, proposes plans, writes code, runs tests and reports changes. Its project rules live in the root `AGENTS.md`.
- **You = technical owner.** You approve scope and architecture decisions, inspect the important code paths, run the application, review evidence quality, and commit completed work.

Neither agent may silently add features or change a locked architecture decision. All meaningful changes are recorded in `docs/DECISIONS.md`.

## 3. Read these files in order

1. `PROJECT_CHARTER.md` — problem, target user, exact MVP and non-goals.
2. `ARCHITECTURE.md` — selected stack, data flow, schema, API and MCP design.
3. `EXECUTION_PLAN.md` — eight-week sequence and exit criteria.
4. `CHATGPT_PROJECT_INSTRUCTIONS.md` — paste this into the ChatGPT Project instructions.
5. `AGENTS.md` — copy this to the root of the Git repository for OpenCode.
6. `WORKFLOW_TEMPLATES.md` — prompts and the repeatable ChatGPT → OpenCode → review loop.
7. `EVALUATION_SECURITY_DEPLOYMENT.md` — evaluation, safety, legal, deployment and release gates.
8. `DECISIONS.md` and `PROJECT_STATE.md` — initial decisions and current status.
9. `SOURCE_REGISTER.md` — official technical references and licensing notes.

## 4. Setup in the new ChatGPT Project

1. Create a ChatGPT Project named **ReplicateLens — Research Reproducibility Intelligence**.
2. Paste the contents of `CHATGPT_PROJECT_INSTRUCTIONS.md` into the Project instructions.
3. Upload the project handoff files, at minimum `PROJECT_CHARTER.md`, `ARCHITECTURE.md`, `EXECUTION_PLAN.md`, `DECISIONS.md`, `PROJECT_STATE.md`, `EVALUATION_SECURITY_DEPLOYMENT.md`, and `SOURCE_REGISTER.md`.
4. Start the first conversation with the kickoff prompt below.
5. Keep project conversations inside this Project. At every milestone, update `PROJECT_STATE.md` and upload/replace the latest version in the Project so its context remains current.

### First message to the ChatGPT Project

```text
We are starting ReplicateLens. Treat the uploaded project documents as the source of truth. First, read the project charter, architecture, execution plan, decisions, evaluation/security plan, and source register. Do not write application code yet. Confirm the locked product scope, identify any contradictions or unresolved decisions, and give me the exact Week 0 setup checklist. Do not broaden the scope. Ask only questions that block a decision; otherwise use the defaults in the documents and record assumptions.
```

## 5. Setup in the code repository

Create a new GitHub repository named `replicatelens` (or another available name), clone it locally, and copy this handoff's `AGENTS.md` into its root. Add a `docs/` folder and place the project charter, architecture, decisions, evaluation plan and project state there. Create `.env.example`; never commit real secrets.

Open the repository in OpenCode. Ask it to inspect the workspace and propose the initial skeleton **without editing files**. Review that plan in ChatGPT Project before allowing implementation. OpenCode supports persistent `AGENTS.md` instructions and local/remote MCP configuration; follow its current documentation rather than copying an old V1 configuration format.

## 6. The repeatable development loop

For every feature:

1. Ask ChatGPT Project for the next smallest task, with acceptance criteria and out-of-scope boundaries.
2. Give OpenCode the task. First request an inspection-only plan.
3. Review the plan. Do not let it edit unrelated modules.
4. Ask OpenCode to implement only the approved task.
5. Ask it to run focused tests, lint/type checks and report exact commands/results.
6. Review the diff and test output. Share them with ChatGPT Project for an independent review.
7. Fix issues, rerun tests, manually verify the user flow, then commit.
8. Update `PROJECT_STATE.md`, `DECISIONS.md` if applicable, and the task backlog.

Never mark a feature complete because an agent says it is complete. A feature is complete only when its acceptance criteria pass and the result has been manually checked.

## 7. Eight-week target

| Week | Exit deliverable |
|---|---|
| 0 | Requirements, repository, environment, CI and architecture locked |
| 1 | Paper resolution and PDF text extraction work end-to-end |
| 2 | Scholarly metadata ingestion and repository candidate discovery work |
| 3 | Static GitHub repository inspection produces file-level evidence |
| 4 | Structured implementation-readiness report works |
| 5 | Evidence-grounded RAG Q&A works and is evaluated |
| 6 | Custom MCP server works locally in OpenCode |
| 7 | Public-facing website and seeded demo are complete |
| 8 | Deployment, security checks, evaluation, documentation and demo are complete |

The week plan is a target, not permission to skip quality gates. If time is tight, cut stretch features—not tests, security, source citations or the evidence model.

## 8. Definition of success

A public user can open a demo paper, inspect a linked repository, read an evidence-backed report, ask a question with source references, and connect an MCP-compatible client to ReplicateLens's read-only server. The README explains the architecture, limitations, evaluation results and deployment. A short demo video shows the complete flow.

**The MVP is successful if it is trustworthy and demonstrable—not if it has the largest feature count.**


---

# FILE: PROJECT_CHARTER.md

# ReplicateLens — Project Charter & Scope Contract

## 1. Product statement

**ReplicateLens is an AI research implementation intelligence platform.** It connects academic papers to public code repositories and extracts evidence about how a method is implemented, what is required to run it, and what information is missing or ambiguous.

### One-sentence value proposition

Help researchers and engineers move from “I found a paper” to “I understand the available implementation evidence and know what I need to reproduce or extend it.”

## 2. The problem

Research implementation work is fragmented across paper PDFs, scholarly metadata services, GitHub repositories, README files, dependency manifests, configuration files, issues and dataset pages. A researcher manually visits each location and must decide whether the repository is official, whether the code corresponds to the paper, and whether the instructions provide enough information to attempt an implementation.

ReplicateLens reduces that manual investigation by collecting permitted public metadata, finding repository candidates, statically inspecting selected repository files, and producing a transparent report with evidence links.

## 3. Target user and initial use case

**Primary user:** An ML/AI student, researcher or engineer evaluating a paper before attempting to implement or extend it.

**Primary user story:**

> As a researcher, I want to submit a paper and inspect its likely official implementation so that I can understand the required environment, datasets, checkpoints, training/evaluation entry points and documented gaps before spending time setting it up.

Initial domain focus for demo content: machine learning, computer vision and multimodal AI. The data model must remain general enough for other research areas, but we will curate the initial examples in fields where implementation repositories are common.

## 4. The core workflow

1. **Submit paper:** user provides an arXiv identifier/URL, DOI, supported open-access paper URL, or selects a seeded demo paper.
2. **Resolve paper:** retrieve title, authors, abstract, publication date, identifiers, canonical URL and open-access PDF URL where available.
3. **Parse paper:** extract text, sections and references where possible. Preserve page/section locations when available.
4. **Find repositories:** extract explicit GitHub links from paper metadata/text first. If no explicit link exists, search GitHub using title, authors and distinctive terms; return candidates with reasons and confidence signals.
5. **Confirm match:** user selects/confirm the repository. Never silently label a fuzzy search result as the official implementation.
6. **Inspect repository:** retrieve only public metadata and a bounded set of relevant text files through the GitHub API. Do not execute repository code.
7. **Extract evidence:** identify dependencies, language/runtime, CUDA mentions, datasets, checkpoints, configuration files, training/evaluation entry points, commands and documentation gaps. Every extracted fact must retain a source pointer.
8. **Generate report:** compare paper-described requirements with repository evidence and assign transparent evidence statuses.
9. **Ask questions:** user asks questions against the paper and repository evidence; answers cite the exact paper/repository source.
10. **Expose via MCP:** compatible AI clients can call the read-only research tools and retrieve reports/evidence.

## 5. MVP features — must ship

### F1. Paper input and metadata resolution
- Support arXiv ID/URL and DOI in the first release.
- Support direct open-access PDF URLs only when they resolve to permitted public content.
- Resolve basic metadata using arXiv, OpenAlex and Crossref adapters.
- Deduplicate using DOI, arXiv ID and normalised title/author fallback.
- Show the metadata source and canonical paper link.

### F2. PDF text extraction
- Use `pypdf` as the baseline parser for text and metadata.
- Extract page-level text and retain page numbers.
- Normalise whitespace and identify likely section headings with conservative heuristics.
- Handle scanned/image-only PDFs as unsupported in the MVP; show a clear message rather than silently generating an empty report.
- Keep a parser adapter boundary so GROBID can be added later.

### F3. Repository discovery and confirmation
- Detect explicit GitHub links in the paper metadata/text.
- Search for candidates only when needed.
- Return up to five candidates with repository name, description, URL, match reasons and a transparent confidence indicator.
- Require user confirmation when the paper does not explicitly identify a repository.
- Store why a repository was considered official/associated and whether a human confirmed it.

### F4. Static repository inspector
Inspect public GitHub repository metadata and a bounded list of files, prioritising:
- `README` / `README.md`
- `LICENSE`
- `requirements.txt`, `pyproject.toml`, `setup.py`, `environment.yml`, `conda.yml`
- `Dockerfile`, `docker-compose.yml`
- configuration files (YAML/TOML/JSON)
- likely train/evaluate/inference scripts
- checkpoint and dataset documentation/link references
- GitHub Actions workflow files, where useful

Limits for the first release: configurable maximum file count, total bytes, per-file bytes, API requests and processing time. Save path, SHA/commit reference, retrieval time and line ranges. Never clone and run arbitrary repositories.

### F5. Evidence extraction
Extract structured evidence for:
- programming language and framework versions
- Python version and dependency declarations
- CUDA/cuDNN/GPU requirements when explicitly stated
- dataset names and download/setup references
- model checkpoint/pretrained weight links
- training, evaluation and inference scripts
- documented commands
- configuration files and relevant settings
- seeds, metrics and benchmark references where explicitly present
- unresolved or conflicting instructions

Each evidence item stores: claim type, extracted value, status, source URL, repository path or paper page/section, line/page range when available, short supporting excerpt, extraction method, confidence and timestamp.

### F6. Reproducibility/implementation-readiness report
Report sections:
1. Paper overview and canonical links
2. Research problem and claimed contribution (carefully summarised)
3. Method/task and datasets/metrics named in the paper
4. Repository match and why it was selected
5. Environment and dependency evidence
6. Data and checkpoint evidence
7. Train/evaluate/inference entry points and documented commands
8. Paper-to-repository consistency notes
9. Missing, ambiguous or conflicting information
10. Recommended manual next steps
11. Full source/evidence references

Evidence status vocabulary is mandatory:
- **Verified:** directly supported by a retrieved source.
- **Not found in inspected sources:** the bounded inspection did not locate it; this does not prove it does not exist elsewhere.
- **Ambiguous:** sources conflict or support multiple interpretations.
- **Inferred:** plausible interpretation generated from indirect evidence; must be visually distinguished and never presented as verified.

Do not create an opaque “reproducibility probability” score. A simple documentation coverage indicator may be considered only if its formula and denominator are explicit. It must not be described as probability of successful reproduction.

### F7. Evidence-grounded Q&A
- Answer questions using only retrieved paper/repository evidence for project-specific factual claims.
- Cite paper page/section or repository path and line ranges wherever available.
- Explicitly say when evidence is insufficient.
- Do not invent dependency versions, dataset access, benchmark results or commands.

### F8. Custom MCP server
Expose a small, read-only set of tools, resources and prompts. The server must call the same application services as the website; do not implement duplicate business logic inside MCP handlers.

Minimum tools:
1. `search_papers`
2. `get_paper_overview`
3. `find_repository_candidates`
4. `get_repository_evidence`
5. `get_reproducibility_report`
6. `search_evidence`

Minimum resources:
- `replicatelens://papers/{paper_id}`
- `replicatelens://reports/{report_id}`

Minimum prompts:
- `assess_implementation_readiness`
- `compare_paper_and_repository_evidence`

Tools are read-only in the public MVP. Expensive report-generation actions are initiated through the website's bounded job workflow, not exposed to unauthenticated MCP clients.

### F9. Public demo website
- Landing page explaining the problem and workflow.
- Search/submit page for supported identifiers.
- Research workspace/report detail page.
- Evidence browser with source links.
- Q&A panel for a report.
- MCP integration/documentation page.
- At least three curated, precomputed demo cases with known paper/repository associations.
- Clear “demo” status and limitations.

## 6. Explicit non-goals — do not build in the eight-week MVP

- Automatically cloning and executing arbitrary GitHub repositories.
- Running training jobs or promising successful reproduction.
- Generating an entire codebase from a paper.
- Autonomous debugging and patching of third-party repositories.
- General-purpose academic search across every discipline at web scale.
- Bulk crawling of publisher websites or paywalled papers.
- Scraping private repositories or bypassing authentication.
- User social network, comments, collaborative annotation or paper-sharing community.
- Subscription/payment system.
- Mobile application/browser extension.
- Multi-provider LLM routing unless the single-provider implementation is stable.
- Advanced citation graph visualisation.
- Automatic benchmarking or model leaderboard.
- Complex multi-agent framework.
- Fine-tuning a model.

These can only be reconsidered after the MVP passes its release gates and only through a written scope decision.

## 7. Success criteria

The MVP is complete when a user can:
1. Submit a supported paper identifier and receive normalized metadata.
2. View candidate repositories and confirm the correct one.
3. Inspect bounded repository files without executing them.
4. Generate a report where factual claims link to evidence.
5. Ask questions and receive grounded answers with source pointers.
6. Use the same read-only functions through the custom MCP server.
7. Open a public demo site and complete the full flow on at least three curated examples.

Quality targets are defined in `EVALUATION_SECURITY_DEPLOYMENT.md`. They are targets to test, not claims to put on a resume before measuring.

## 8. Scope change rule

Any proposed feature must answer all four questions:
1. Does it directly improve the primary paper-to-implementation workflow?
2. Is it required for the eight-week MVP or a release blocker?
3. What will be removed or delayed to make room for it?
4. How will it be tested?

If there is no strong answer, put the feature in `docs/POST_MVP_BACKLOG.md` and continue the existing plan.


---

# FILE: ARCHITECTURE.md

# ReplicateLens — Technical Architecture

## 1. Architecture principles

1. **Evidence before prose:** LLMs may interpret evidence, but they do not create source facts.
2. **API-first, scrape selectively:** use official scholarly/GitHub APIs for structured data; scrape only public pages that lack suitable APIs.
3. **Human confirmation for uncertain repository matches:** automated matching proposes; a human confirms.
4. **Static inspection only:** never execute source code or install dependencies from a third-party repository.
5. **One application service layer:** REST routes, MCP tools and workers call the same services.
6. **Small operational footprint:** one web frontend, one Python backend, one worker, one PostgreSQL database and one Redis-compatible queue.
7. **Provider boundary without premature abstraction:** use a small `LLMProvider` interface and one configured provider for the MVP.

## 2. Recommended stack (MVP defaults)

| Layer | Choice | Reason |
|---|---|---|
| Frontend | Next.js App Router + TypeScript | Public, responsive web application |
| UI | Tailwind CSS + shadcn/ui | Reusable dashboard components |
| Backend | Python 3.12 + FastAPI + Pydantic v2 | Existing user strengths; typed HTTP API |
| Python environment | `uv` | Reproducible dependency and lockfile workflow |
| ORM/migrations | SQLAlchemy 2 + Alembic | Typed data access and schema migrations |
| HTTP integrations | HTTPX | Async API requests, timeouts and retries |
| HTML parsing | BeautifulSoup | Targeted parsing of permitted public pages |
| Browser automation | Playwright only when a specific public page requires JS | Avoid browser use as the default crawler |
| PDF extraction | `pypdf` | Simple baseline, BSD-3-Clause licensing |
| Scholarly metadata | arXiv API, OpenAlex, Crossref | Complementary public metadata sources |
| Repository metadata/content | GitHub REST API | Structured repository/tree/file retrieval |
| Database | PostgreSQL | Relational evidence and job state |
| Vector search | pgvector + PostgreSQL full-text search | One database for metadata and retrieval |
| Embeddings | Small local sentence-transformer model (start with `all-MiniLM-L6-v2`, 384 dimensions) | Avoid a second paid embedding API in the MVP; evaluate retrieval quality |
| LLM | One chosen API provider behind a small adapter | Control cost and avoid provider-specific code spread |
| Background jobs | RQ + Redis-compatible queue | Keep PDF/repository/LLM work outside request handlers |
| MCP | Official Python MCP SDK, v2 major line | Custom tools/resources/prompts and client compatibility |
| Tests | pytest, Ruff, mypy; frontend lint/typecheck/build | Automated verification |
| Local services | Docker Compose | Consistent PostgreSQL/Redis development setup |
| Deployment target | Vercel frontend; Render API/worker; Supabase Postgres with pgvector | Straightforward public hosting; verify current plan limits/pricing before deploying |

**LLM provider decision:** choose one provider during Week 0 based on model quality, API access and an explicit spending limit. Do not assume a ChatGPT subscription covers API calls: ChatGPT and API billing are separate, although certain eligible plans may have a separate API-credit benefit. Verify current eligibility and API billing before selecting the provider. Keep model name and provider credentials in environment variables. Do not embed keys in source code or the browser.

## 3. Reuse vs custom engineering

### Reuse
- arXiv/OpenAlex/Crossref for scholarly metadata.
- GitHub REST API for repository metadata, trees, files and releases.
- `pypdf` for baseline PDF text extraction.
- pgvector for vector storage/search.
- Official MCP Python SDK for protocol implementation.
- PaperQA2 as an optional RAG benchmark/spike after the custom baseline exists.
- GROBID as an optional structural PDF parser if the baseline parser proves inadequate and hosting resources permit it.

### Build ourselves
- Paper identifier normalization and source adapters.
- Paper-to-repository candidate discovery/ranking with inspectable reasons.
- Human-confirmed paper/repository association.
- Bounded static repository inspector.
- Evidence extraction schema and validation.
- Paper-vs-repository evidence comparison.
- Transparent implementation-readiness report.
- Evidence-grounded Q&A and citation mapping.
- ReplicateLens MCP tools/resources/prompts.
- User-facing research workspace and deployment.

### Why not put PaperQA2 and GROBID in the critical path?
PaperQA2 already offers an agentic scientific-literature RAG workflow; integrating it is a useful experiment, but building the product around it would not solve repository matching or code evidence extraction. GROBID provides richer scholarly structure but its full-text Docker deployment can require several GB of RAM. Start with `pypdf`, keep an adapter boundary, and add GROBID only if measured extraction quality justifies the added operational load.

## 4. High-level system layout

```text
Browser (Next.js)
   |
   v
FastAPI application -------------------- Custom MCP endpoint
   |                                         |
   |                                         v
   |                                  MCP tools/resources/prompts
   |                                         |
   +--------------- shared service layer ---+
                     |
          +----------+-----------+
          |          |           |
       Scholarly   GitHub     Evidence/RAG
       providers   adapter      services
          |          |           |
          +----------+-----------+
                     |
             PostgreSQL + pgvector
                     ^
                     |
             RQ worker + Redis
                     |
          PDF parsing / repo inspection /
          structured extraction / report
```

MCP handlers must not reimplement logic. They validate input, call the relevant service, and return compact structured results with source references.

## 5. Ingestion and report pipeline

### Step A — Input normalization
- Accept only supported paper identifiers/URLs: arXiv, DOI, approved open-access sources.
- Parse and normalize identifier.
- Reject unsupported URL schemes and arbitrary local/private network destinations.
- Store original user input and normalized identifier separately.

### Step B — Metadata resolution
- arXiv ID → arXiv API.
- DOI → Crossref and/or OpenAlex.
- Search/title fallback → OpenAlex (and optional Semantic Scholar later).
- Deduplicate using DOI/arXiv ID; title+author matching is fallback only.
- Store each provider response timestamp and canonical source URL.

### Step C — PDF acquisition and parsing
- Prefer an open-access PDF URL supplied by the metadata provider.
- Do not bypass paywalls or access controls.
- Start with `pypdf`; extract page text and metadata.
- Detect empty/scanned PDF and fail gracefully with an actionable message.
- Preserve page numbers and a normalized section representation.
- Do not publicly redistribute full PDFs. Store metadata and derived evidence according to source/license terms.

### Step D — Repository discovery
1. Extract GitHub repository links explicitly referenced by paper metadata/text.
2. If no explicit repo link exists, search GitHub using title, author names and distinct terms.
3. Score candidates using transparent signals: explicit paper link, title overlap, author/organization relationship, topic/README overlap, repository description and activity metadata.
4. Show candidate reasons and uncertainty.
5. Require user confirmation if not explicit.

### Step E — Static repository inspection
- Use GitHub REST API and a read-only token where appropriate.
- Read the repository metadata and default branch/tree.
- Fetch only a bounded allowlist of likely informative files.
- Prefer GitHub API contents/raw blobs; do not clone the repository in the hosted service.
- Record commit SHA, file path, file SHA, retrieval timestamp and content hash.
- Apply maximum repository file count, file-size, total-byte and time budgets.
- Cache by repository + commit SHA; refresh only when commit changes or the user requests refresh.

### Step F — Evidence extraction
Use deterministic extractors first for obvious facts (dependency files, Python version declarations, config files, GitHub Actions runtime, explicit URLs). Use the LLM for interpretation and classification, not as sole source of truth.

Return structured JSON validated by Pydantic. Each evidence item must refer to a source object and exact location. If the LLM supplies a path or line range that does not exist in retrieved content, reject it or mark it unsupported.

### Step G — Paper-to-repository comparison
Compare only documented facts:
- paper-stated dataset vs repository dataset references
- paper-stated training/evaluation procedure vs repository entry points
- paper-stated model/checkpoint vs repository checkpoint references
- reported metrics vs scripts/configs/documentation mentioning them
- environment requirements vs dependency declarations

Do not infer that the code faithfully implements the paper merely because names match. Clearly separate “documented in repository” from “verified implementation behavior.”

### Step H — Report and Q&A
- Generate a structured report from validated evidence objects.
- Store report version and source snapshot references.
- Chunk paper text and relevant repository files for retrieval.
- Use hybrid retrieval: PostgreSQL full-text search + pgvector similarity.
- Every factual answer must return source links/locations. If retrieval returns insufficient evidence, say so.

## 6. Proposed data model

Use UUID primary keys unless an external identifier is naturally stable.

| Table | Important fields |
|---|---|
| `papers` | `id`, `arxiv_id`, `doi`, `title`, `abstract`, `authors_json`, `published_at`, `canonical_url`, `pdf_url`, `metadata_json`, `created_at` |
| `paper_sources` | `paper_id`, `provider`, `source_url`, `retrieved_at`, `raw_metadata_hash` |
| `repositories` | `id`, `github_full_name`, `html_url`, `description`, `default_branch`, `license_spdx`, `stars`, `pushed_at` |
| `paper_repository_links` | `paper_id`, `repository_id`, `match_method`, `match_reasons_json`, `confidence`, `human_confirmed`, `confirmed_at` |
| `repository_snapshots` | `repository_id`, `commit_sha`, `tree_sha`, `retrieved_at`, `snapshot_hash` |
| `repository_files` | `snapshot_id`, `path`, `file_sha`, `content_hash`, `text_content`, `size_bytes`, `retrieved_at` |
| `evidence_items` | `paper_id`, `repository_id`, `report_id`, `claim_type`, `value_json`, `status`, `source_type`, `source_url`, `path`, `page_number`, `line_start`, `line_end`, `excerpt`, `confidence`, `extraction_method` |
| `reports` | `id`, `paper_id`, `repository_id`, `status`, `report_json`, `version`, `created_at` |
| `document_chunks` | `id`, `source_type`, `source_id`, `text`, `metadata_json`, `embedding vector(384)` |
| `jobs` | `id`, `job_type`, `status`, `input_json`, `result_json`, `error_code`, `created_at`, `updated_at` |

Keep user/account tables out of the first release unless a real product requirement emerges. A public demo can use precomputed public examples and rate-limited analysis rather than adding a complete multi-user product.

## 7. Evidence object contract

Minimum Pydantic shape (field names can change through an ADR, but concepts are required):

```json
{
  "claim_type": "python_version",
  "value": "3.10",
  "status": "verified",
  "source": {
    "kind": "repository_file",
    "url": "https://github.com/owner/repo/blob/<sha>/environment.yml",
    "path": "environment.yml",
    "line_start": 1,
    "line_end": 4
  },
  "excerpt": "python=3.10",
  "extraction_method": "deterministic_parser",
  "confidence": 1.0,
  "notes": null
}
```

The excerpt should be short and sufficient to support the claim. If line numbers cannot be reliably mapped, store a file path and a clearly labelled snippet rather than inventing line numbers.

## 8. API boundary (initial proposal)

REST routes are versioned under `/api/v1`.

- `POST /papers/resolve` — submit a supported paper identifier; returns metadata or a job ID.
- `GET /papers/{paper_id}` — get normalized metadata.
- `POST /papers/{paper_id}/repository-candidates` — discover candidates.
- `POST /papers/{paper_id}/repository-confirmation` — confirm selected repository.
- `POST /papers/{paper_id}/analysis-jobs` — enqueue bounded inspection/report generation.
- `GET /jobs/{job_id}` — poll job status.
- `GET /reports/{report_id}` — get structured report.
- `GET /reports/{report_id}/evidence` — list evidence items.
- `POST /reports/{report_id}/questions` — ask grounded question.
- `GET /health` and `GET /ready` — health/readiness.

Exact naming is not frozen until Week 0 API contract design. Keep responses typed and errors predictable.

## 9. Custom MCP contract

MCP tools should be compact, read-only and useful to an external host. Tool arguments are validated by Pydantic or SDK schema; results return structured content plus concise human-readable summaries.

Tools:
- `search_papers(query, limit)`
- `get_paper_overview(paper_id)`
- `find_repository_candidates(paper_id)`
- `get_repository_evidence(paper_id, repository_id)`
- `get_reproducibility_report(report_id)`
- `search_evidence(paper_id, query, limit)`

Resources:
- `replicatelens://papers/{paper_id}`
- `replicatelens://reports/{report_id}`

Prompts:
- `assess_implementation_readiness`
- `compare_paper_and_repository_evidence`

Use stdio transport for local development/client integration first; deploy remote Streamable HTTP after the tool contract is tested. Validate Origin and implement authentication/rate limiting for remote HTTP. Public remote tools should expose only public demo/cache data and must not allow arbitrary expensive analysis without authorization.

## 10. Suggested repository structure

```text
replicatelens/
├── AGENTS.md
├── README.md
├── .env.example
├── .gitignore
├── docker-compose.yml
├── frontend/
│   ├── app/
│   ├── components/
│   ├── lib/
│   └── tests/
├── backend/
│   ├── pyproject.toml
│   ├── uv.lock
│   ├── replicatelens/
│   │   ├── api/v1/
│   │   ├── core/
│   │   ├── db/
│   │   ├── models/
│   │   ├── schemas/
│   │   ├── providers/scholarly/
│   │   ├── providers/github/
│   │   ├── providers/pdf/
│   │   ├── services/
│   │   ├── pipelines/
│   │   ├── retrieval/
│   │   ├── mcp/
│   │   └── workers/
│   └── tests/
├── docs/
│   ├── PROJECT_CHARTER.md
│   ├── ARCHITECTURE.md
│   ├── EXECUTION_PLAN.md
│   ├── DECISIONS.md
│   ├── PROJECT_STATE.md
│   ├── EVALUATION.md
│   ├── SECURITY.md
│   └── POST_MVP_BACKLOG.md
└── .github/workflows/ci.yml
```

The layout is a starting point, not a mandate to create empty abstractions. OpenCode should create modules when the first real feature needs them.


---

# FILE: EXECUTION_PLAN.md

# ReplicateLens — Eight-Week Execution Plan

**Planning assumption:** approximately 10–15 focused hours each week. The target is a functional, publicly hosted MVP, not a production-scale commercial platform. Every week has a demoable exit condition. Do not begin the next major phase with a broken previous phase.

## Week 0 — Kickoff and environment

### Work
- Create ChatGPT Project and install its instructions.
- Create GitHub repository and local working directory.
- Copy root `AGENTS.md` from handoff.
- Create `docs/` and add charter, architecture, decisions, state and evaluation plan.
- Choose one LLM API provider and set a small monthly usage cap/budget alert.
- Create `.env.example`; keep actual `.env` ignored.
- Decide local Python/package tooling: Python 3.12 + `uv`; Node LTS + `pnpm`.
- Define initial design tokens and rough wireframes; no detailed UI implementation yet.
- Set up Docker Compose for local PostgreSQL/pgvector and Redis.
- Configure GitHub Actions CI skeleton.

### Deliverables
- Reproducible local development setup.
- `README.md`, `AGENTS.md`, `.env.example`, docs and CI baseline.
- One ADR for each unresolved architectural choice.

### Exit gate
A fresh clone can follow the setup instructions, start local dependencies, run a health check and run a trivial test. No secrets are in Git.

## Week 1 — Paper resolution vertical slice

### Work
- Implement identifier parser for arXiv ID/URL and DOI.
- Implement scholarly provider interfaces and arXiv + Crossref/OpenAlex adapters.
- Normalize metadata into Pydantic schema and persist `papers`/`paper_sources`.
- Implement PDF link detection for supported open-access sources.
- Download one allowed PDF safely with content-size/time limits.
- Extract text and page numbers using `pypdf`.
- Detect unsupported scanned PDFs and display a clear state.
- Add API endpoints and tests.

### Deliverables
- User submits an arXiv ID/DOI and receives metadata.
- Paper record is persisted and deduplicated.
- One open-access PDF can be parsed page by page.

### Exit gate
Automated tests cover valid/invalid identifiers, provider errors, duplicate identifiers, missing PDF and empty/scanned PDF. Manually test at least three papers.

## Week 2 — Repository discovery

### Work
- Implement GitHub API client using HTTPX and server-side token.
- Parse explicit repository links from paper metadata/text.
- Build fallback candidate search from title/authors/key phrases.
- Create deterministic ranking signals and explainable match reasons.
- Add repository-candidate UI/API schema.
- Add a human confirmation step and persist the confirmed relationship.
- Cache metadata and respect GitHub rate limits.

### Deliverables
- Paper produces up to five repository candidates.
- User can confirm the right repository or mark “no repository found.”

### Exit gate
Create a small hand-labelled paper/repository test set. For papers with an explicit link, the explicit link must outrank fuzzy candidates. Fuzzy candidates must never be auto-labelled official.

## Week 3 — Static repository inspector

### Work
- Fetch repo metadata, default branch, tree and selected file contents.
- Implement file priority/allowlist and bounded limits.
- Inspect README, licenses, Python manifests, environment files, Docker files, config, train/eval/inference scripts and workflow files.
- Map fetched content to commit SHA, file path, blob SHA, timestamp and hash.
- Parse dependency/runtime/dataset/checkpoint/entry-point signals deterministically where possible.
- Add cache keyed by repo commit SHA.

### Deliverables
- Confirmed repository can be inspected without cloning/executing it.
- Evidence candidates point to exact files and line ranges when possible.

### Exit gate
Test with at least three repositories of different structures. Verify missing files, large files, rate limits, private repos and unsupported file types fail safely.

## Week 4 — Evidence engine and report

### Work
- Finalize evidence schema and status vocabulary.
- Implement deterministic extraction for manifests/configs/commands/URLs.
- Add LLM structured extraction for less deterministic interpretation.
- Validate all model-proposed references against retrieved files/pages.
- Build report generator and report persistence/versioning.
- Implement paper-vs-repository comparison for dataset, environment, checkpoints and train/eval entry points.
- Design a readable report UI prototype.

### Deliverables
- End-to-end paper → confirmed repo → evidence → structured report.
- Every factual claim is labelled and has a source pointer or is explicitly “inferred.”

### Exit gate
Manually audit at least five reports. No unsupported claim may be presented as verified. “Not found” wording must say “not found in inspected sources.”

## Week 5 — RAG and grounded Q&A

### Work
- Chunk paper text and selected repository evidence with source metadata.
- Generate embeddings using the chosen small local embedding model.
- Store vectors with pgvector; add PostgreSQL full-text search.
- Implement hybrid retrieval and citation mapping.
- Build Q&A endpoint and report-scoped context boundaries.
- Add “insufficient evidence” response behaviour.
- Create an evaluation set of questions with expected source documents.
- Optionally run a small PaperQA2 comparison spike only if Weeks 1–4 are stable; do not integrate by default.

### Deliverables
- User asks questions about one paper/repository and gets a grounded answer with source links.

### Exit gate
Run a manually reviewed Q&A set. Verify citations lead to the retrieved source and no model-only claim is shown as fact. Measure retrieval and citation performance.

## Week 6 — Custom MCP server

### Work
- Implement MCP server using official Python SDK v2 major line.
- Reuse application service functions for MCP tools.
- Implement six read-only tools, two resources and two prompts from `ARCHITECTURE.md`.
- Add tool input validation, compact outputs, error handling and request limits.
- Test with the SDK's in-memory/client test harness.
- Expose local stdio transport and connect it to OpenCode.
- Document how an external MCP client can configure the local server.
- Add Streamable HTTP deployment mode only after local interoperability works.

### Deliverables
- OpenCode can discover the ReplicateLens local MCP tools and retrieve a seeded or generated report.

### Exit gate
Each tool has a contract test. Invalid IDs, unknown papers and service errors return safe, structured errors. MCP uses only the same trusted service layer as the REST API.

## Week 7 — Website and demo experience

### Work
- Build responsive landing page and application shell.
- Build search/submit page with supported input types.
- Build report page with tabs/sections for paper, repository, environment, datasets/checkpoints, commands and evidence gaps.
- Add source links, page/line references, and evidence-status badges.
- Add report-scoped AI chat.
- Create at least three curated, precomputed demo cases (prefer open-access papers with clearly associated public repositories).
- Add MCP setup/documentation page.
- Add progress/error/loading states and mobile-responsive layout.

### Deliverables
- A visitor can complete a full demo flow on the website.

### Exit gate
Manual browser test on desktop/mobile. No broken links, dead controls or unsupported marketing claims. Demo examples have manually checked source associations.

## Week 8 — Production readiness and release

### Work
- Deploy frontend, API, worker, database and MCP endpoint.
- Configure domains, HTTPS, environment variables and secret handling.
- Configure queue, retries, timeouts, API rate limits and request-size limits.
- Add basic error monitoring and health/readiness checks.
- Run full backend/frontend/MCP tests and security checklist.
- Evaluate the curated paper/repository set and report metrics honestly.
- Write README, architecture diagram, setup guide, limitations, data sources and API/MCP guide.
- Record a 2–4 minute demo video.
- Prepare resume bullets using only measured facts.

### Deliverables
- Public website, public repository, working MCP demo, documentation and evaluation results.

### Exit gate
A fresh clone can be started from docs; public site works; sample reports are grounded; external MCP client can connect; no secrets are exposed; all critical tests pass.

## Critical path

Paper resolution → PDF extraction → repo discovery/confirmation → static repo inspection → evidence extraction → report → RAG → MCP → UI integration → deploy.

Do not spend the first two weeks polishing frontend or comparing UI libraries. Build a thin end-to-end backend path first, then expose it in the website.

## Scope cuts if time slips

Cut in this order:
1. Semantic Scholar integration.
2. GROBID integration.
3. PaperQA2 comparison spike.
4. Paper-to-paper comparison.
5. Complex dashboards/visualisations.
6. User accounts and personal saved workspaces.
7. On-demand public arbitrary analysis; retain seeded demo only.

Do **not** cut:
- human confirmation for uncertain repository matches
- source-backed evidence statuses
- no-execution security rule
- tests for critical flows
- report citations
- MCP tool contract tests
- deployed demo and accurate documentation


---

# FILE: CHATGPT_PROJECT_INSTRUCTIONS.md

# ChatGPT Project Instructions — ReplicateLens

You are the **project overseer, product owner, architecture reviewer and scope guardian** for ReplicateLens. OpenCode is the primary coding agent. The user is the technical owner and makes final decisions.

## Project objective

Build an eight-week public MVP: an AI research implementation intelligence platform. A user submits an arXiv ID/URL or DOI; the app resolves scholarly metadata, finds likely GitHub implementations, asks the user to confirm uncertain repository matches, statically inspects bounded public repository files, extracts source-linked evidence, produces a transparent implementation-readiness report, supports evidence-grounded Q&A, and exposes read-only research capabilities through a custom MCP server.

It is **not** an automatic paper-reproduction or arbitrary code-execution service.

## Source of truth

Treat the uploaded `PROJECT_CHARTER.md`, `ARCHITECTURE.md`, `EXECUTION_PLAN.md`, `DECISIONS.md`, `PROJECT_STATE.md`, and `EVALUATION_SECURITY_DEPLOYMENT.md` as authoritative. If chats conflict with these files, call out the conflict and follow the latest explicitly approved decision. Do not rely on stale assumptions from earlier conversations. Ask for current `git status`, relevant diffs, tests and logs before making claims about implementation state.

## Responsibilities

1. Maintain the product scope and eight-week plan.
2. Convert goals into small, bounded OpenCode tasks with acceptance criteria.
3. Review architecture/dependency changes before implementation.
4. Inspect code diffs, tests and evidence quality when provided.
5. Identify security, privacy, licensing, cost and reliability risks.
6. Maintain `PROJECT_STATE.md`, `DECISIONS.md`, evaluation results and backlog summaries.
7. Help the user understand the code and explain the system in interviews.

## Non-negotiable scope rules

- No arbitrary repository code execution, `pip install` from inspected repos, shelling out to user-provided scripts, or automatic training runs in the MVP.
- No bypassing paywalls, authentication, robots restrictions or API limits.
- Use APIs for structured data; targeted public-page scraping is a fallback.
- Never label a fuzzy GitHub candidate as official without user confirmation or explicit paper evidence.
- Every factual report claim must point to retrieved evidence or be marked inferred/ambiguous.
- “Not found” must mean “not found in inspected sources,” not “does not exist.”
- MCP public tools are read-only and must use the same application service layer as the website.
- Do not add a dependency or a feature without explaining why it is needed, the operational/license cost, and what it replaces.
- Do not inflate resume claims. Only use implemented, tested and measured results.

## How to manage each task

For every feature request:
1. Check `PROJECT_STATE.md` and identify the current week/milestone.
2. Check whether the task is in scope. If not, recommend deferring it to the post-MVP backlog.
3. Define one small task with: objective, context, exact files/modules likely affected, acceptance criteria, tests, manual verification and explicit non-goals.
4. Ask OpenCode to inspect and propose a plan first. Do not jump directly to broad code generation.
5. Review the plan for scope creep, unnecessary abstractions, dependency choices, security and testability.
6. After implementation, request the diff, test commands and actual output. Do not accept “tests pass” without evidence.
7. Review correctness and source provenance. Pay special attention to claims with no supporting file/page/line.
8. Provide a clear merge/commit recommendation and update the project state.

## Required response structure for coding tasks

- Current milestone and why this task is next.
- Bounded task description.
- Acceptance criteria (checklist).
- Suggested OpenCode prompt.
- Required tests/manual checks.
- Out-of-scope items.
- After the user returns results: review findings, blockers and next task.

## Project management rules

- Keep one active implementation task at a time, except for independent documentation/design tasks.
- Prefer vertical slices that can be demonstrated.
- Do not start a later week until the current exit gate is met or an explicit scope decision is recorded.
- When a new feature is proposed, state what it displaces.
- Maintain an honest status: NOT STARTED, IN PROGRESS, BLOCKED, REVIEW, DONE.
- Every completed milestone must include evidence: commit, tests, screenshot/demo or deployment URL as appropriate.
- At the end of each working session, produce a concise handoff: completed, changed files, test evidence, unresolved decisions, next exact task and OpenCode prompt.

## Technical defaults

Use the architecture document. Current defaults: Next.js + TypeScript; Python 3.12 + FastAPI + Pydantic; PostgreSQL + pgvector; `pypdf` baseline; HTTPX/BeautifulSoup and Playwright only when necessary; GitHub REST API; arXiv/OpenAlex/Crossref; RQ + Redis for jobs; official MCP Python SDK v2; one selected LLM API provider behind an adapter. GROBID and PaperQA2 are optional experiments, not critical path.

## Communication style

Be direct, technically rigorous and candid. Do not praise code without reviewing it. Explain trade-offs and teach the user enough to defend design decisions in an interview. Do not silently change locked architecture or scope. If information is missing, inspect current project artifacts first; ask only a blocking question.


---

# FILE: WORKFLOW_TEMPLATES.md

# ReplicateLens — Working Workflow & Prompt Templates

## 1. The project control loop

### Step 1 — Ask ChatGPT Project for a task

Use:

```text
Review the current ReplicateLens project state and the active milestone. Based only on the locked charter and current implementation evidence, propose the single smallest next task. Include purpose, exact scope, likely files, acceptance criteria, tests, manual verification, risks and non-goals. Do not write code yet. If the task would expand scope, defer it and explain why.
```

### Step 2 — Ask OpenCode to inspect and plan (no edits)

```text
Read AGENTS.md and the relevant project docs. Inspect the current code, tests and git status. Do not modify files yet.

Task: [paste the bounded task from ChatGPT Project]

Return:
1. Current implementation relevant to the task.
2. Proposed minimal changes and exact files.
3. Data/API/schema implications.
4. Edge cases and security considerations.
5. Tests to add/update and commands to run.
6. Risks, assumptions and anything that conflicts with AGENTS.md.

Do not implement until I approve the plan.
```

### Step 3 — Approve implementation

```text
The plan is approved with these constraints: [constraints]. Implement only this task. Do not add unrelated features or refactor unrelated files. Add/update tests. Keep service logic separate from API/MCP adapters. Preserve evidence provenance and all AGENTS.md security rules. At the end, report changed files, tests run with exact output, manual steps still needed, risks and known limitations. Do not commit.
```

### Step 4 — Ask OpenCode for a self-review

```text
Review your changes as if you did not write them. Inspect the full diff. Look for incorrect assumptions, missing edge cases, unsafe URL handling, unbounded network/file/token usage, unsupported evidence claims, poor error handling, missing tests, dependency/license issues and scope creep. Do not change code yet. Return findings grouped by severity with file/line references.
```

### Step 5 — Ask ChatGPT Project for independent review

Provide:
- task and acceptance criteria
- `git diff --stat`
- relevant `git diff`
- test commands and actual output
- screenshots or API/MCP responses for user-facing work

Prompt:

```text
Independently review this implementation against the ReplicateLens charter, architecture and task acceptance criteria. Do not assume OpenCode's summary is correct. Identify correctness, source-grounding, security, reliability, maintainability and scope issues. Classify blockers vs improvements. Tell me whether the task is ready to commit and what evidence is still missing.
```

### Step 6 — Verify and commit

- Run focused tests, then full checks.
- Manually verify user flow/API/MCP client behaviour.
- Inspect `git diff` and `git status`.
- Commit one logical unit with a descriptive message.
- Update `PROJECT_STATE.md` and `DECISIONS.md` when relevant.

## 2. Bug-fix prompt

```text
Investigate this failure without making broad changes. First reproduce it and trace the root cause. Show the smallest likely fix and tests before editing. Do not mask the error, relax validation, or add fallback behaviour that invents evidence. After approval, implement the fix and report the exact regression test and full verification results.

Failure:
[paste stack trace, logs, request/response, reproduction steps]
```

## 3. Architecture/dependency proposal prompt

```text
Evaluate this proposed architecture/dependency change against the eight-week MVP. Compare at least one simpler alternative. Explain licensing, maintenance, security, deployment footprint, cost, compatibility and what existing component it replaces. State what feature/week is delayed if accepted. Do not change code until the user records an ADR.

Proposal: [describe]
```

## 4. End-of-session handoff prompt for ChatGPT Project

```text
Create the end-of-session ReplicateLens handoff. Use only evidence from the files/diff/test outputs I provided. Include:
- current milestone/status
- completed tasks and commit hashes
- changed files and behaviour
- exact test commands/results
- manual verification performed/not performed
- blockers and unresolved questions
- decisions made (with ADR IDs)
- next single task and why it is next
- ready-to-paste OpenCode inspection-only prompt
Update the proposed contents of PROJECT_STATE.md. Do not mark anything done without test/manual evidence.
```

## 5. Weekly review prompt

```text
Review the week's work against the planned exit gate. Give me a factual status (passed/partial/failed), evidence supporting that status, remaining blockers, scope risks, and a recommendation to proceed or stay on the current milestone. If the schedule slipped, propose scope cuts from the documented cut list rather than dropping quality/security gates.
```

## 6. PR/commit checklist

- [ ] Task was in the locked MVP scope.
- [ ] Diff is limited to the approved change.
- [ ] No secrets, tokens or private files are committed.
- [ ] No retrieved repository code is executed.
- [ ] External requests are bounded and handle errors/rate limits.
- [ ] Every report fact has valid evidence/source pointers or an uncertainty label.
- [ ] Tests added/updated and exact results recorded.
- [ ] Frontend/API/MCP manual test completed where applicable.
- [ ] Documentation and project state updated.
- [ ] No unrelated feature or dependency was silently added.


---

# FILE: EVALUATION_SECURITY_DEPLOYMENT.md

# ReplicateLens — Evaluation, Security, Data Rights & Deployment

## 1. Evaluation philosophy

The product's value depends on trust. A report that looks polished but hallucinates a training command or labels the wrong repository as official is a failure. Evaluate source matching, extraction accuracy, evidence coverage and grounded Q&A separately. Do not combine them into an unexplained score.

## 2. Curated evaluation set

Create a manually reviewed benchmark of at least 10 papers with public implementations. Prefer a mix of computer vision, NLP/LLMs and multimodal ML. For each item, record:
- canonical paper ID/URL
- correct repository URL (or explicitly no public repo)
- whether the paper explicitly links the repo
- known dependency/environment signals
- datasets and checkpoints mentioned
- train/evaluation/inference entry points
- 5–10 factual evidence questions with expected source files/pages

A small benchmark is not representative of all scientific research. State its size and selection criteria in the README.

## 3. Proposed MVP metrics and targets

| Metric | How to measure | Initial target |
|---|---|---|
| Repository candidate Hit@3 | Correct repository appears in top three on curated examples | ≥ 80% on the small labelled set, report denominator |
| Explicit-link preservation | Explicit paper-linked repo remains top candidate | 100% on tested explicit-link cases |
| Evidence citation coverage | Factual report assertions with valid source pointers / total factual assertions | ≥ 90% |
| Critical unsupported claims | Critical facts marked verified without valid evidence | 0 in manually audited sample |
| Evidence extraction precision | Correct extracted facts / audited extracted facts | Report per field; target ≥ 90% for deterministic fields |
| Q&A citation correctness | Answers whose cited source supports the associated claim | ≥ 85% on curated test questions before public launch |
| MCP tool contract success | Valid/invalid input cases return expected result/error | 100% of contract tests |
| Pipeline reliability | Successful completion on supported benchmark inputs | ≥ 90%, excluding upstream outages and unsupported scanned PDFs; disclose exclusions |

These are initial engineering targets, not universal research benchmarks. If the target is not met, either improve the implementation or publish the measured limitation honestly.

## 4. Test layers

### Unit tests
- identifier parsing and normalization
- metadata normalization and deduplication
- explicit repository link extraction
- repository candidate scoring
- file path prioritization and size limits
- dependency/config parsing
- evidence schema validation
- status mapping
- prompt/response schema validation

### Integration tests
- mocked arXiv/OpenAlex/Crossref responses
- mocked GitHub API responses, pagination, 403/429/5xx and ETag behaviour
- PDF text extraction for normal, malformed, empty and scanned PDFs
- database migrations, pgvector retrieval and hybrid search
- job retry/failure transitions
- MCP client tool listing/calls/errors

### End-to-end tests
- supported arXiv ID → metadata → PDF → candidate repo → confirmation → report
- DOI → metadata → open-access PDF or graceful no-PDF state
- unknown paper/repo → safe error
- report Q&A → citations to paper/repository evidence
- MCP query → same evidence/report as REST service

### Manual review
For each demo case, inspect the original paper and repository to verify candidate association and report claims. Do not rely only on model-generated self-evaluation.

## 5. Security and abuse controls

### Untrusted content and prompt injection
Paper text, PDF contents, repository READMEs, configuration comments, GitHub issues and external web pages are untrusted data. They may contain instructions designed to manipulate an LLM. Store and pass them as quoted/retrieved evidence, never as system or developer instructions. The model must not be allowed to execute arbitrary actions based on retrieved text.

### Never execute third-party code
- Do not run `setup.py`, notebooks, install commands, shell scripts, Dockerfiles or test scripts from inspected repositories.
- Do not pip-install a dependency list retrieved from a repository.
- Static inspection only for the MVP.
- A later isolated execution sandbox requires a separate threat model, network policy, resource limits, human approval and scope decision.

### SSRF and URL safety
- Support only known scholarly domains and GitHub URLs in the MVP.
- Validate scheme, hostname and redirects.
- Reject localhost, private, link-local, loopback, multicast and cloud metadata destinations.
- Resolve and validate destination IPs; repeat validation after redirects.
- Limit response size, redirect count, content type and timeout.
- Never accept `file://`, `ftp://`, arbitrary localhost or internal service URLs.

### Secrets and access
- Store API tokens in hosting environment variables.
- Keep `.env` out of Git; commit only `.env.example` with placeholder values.
- Use a minimum-scope read-only GitHub token when needed.
- Never send provider keys to the browser or MCP tool results.
- Rotate any key accidentally committed or exposed.

### MCP remote endpoint
- Public tools should be read-only and expose only public/demo content.
- Apply authentication where access to private user data or expensive operations exists.
- Rate-limit requests and set execution timeouts.
- Validate `Origin` on Streamable HTTP and deploy only over HTTPS.
- Do not expose report-generation or arbitrary URL-fetching tools anonymously.

### Frontend/output
- Render Markdown safely; sanitize HTML and external URLs.
- Use parameterized database queries/ORM.
- Validate request body and upload sizes.
- Log job IDs and source identifiers, not secrets or full private document contents.

## 6. Data rights and attribution

- Respect each API's terms, rate limits, caching and attribution requirements.
- Do not bypass publisher paywalls, access controls or authentication.
- Retrieve PDFs only when openly available or supplied by the user with the right to process them.
- Do not publicly redistribute full paper PDFs by default. Link to the publisher/repository source and display derived metadata/evidence consistent with source terms.
- Preserve repository licenses and include repository attribution/source links in reports.
- Use short excerpts only when needed to support a claim; prefer file path/line/page references and links.
- Keep a source register and record the license/terms checks for all reused libraries and datasets.
- For arXiv API use, follow the current API terms and usage guidance and provide the requested attribution for independent non-commercial/open educational use. Re-check if the project becomes commercial or materially changes.

## 7. Deployment target

### Suggested topology
- **Frontend:** Vercel.
- **FastAPI web service:** Render Docker web service.
- **Background worker:** Render background worker.
- **Queue:** Redis-compatible managed service (e.g., Render Key Value).
- **Database:** Supabase Postgres with pgvector extension.
- **MCP:** mount/serve Streamable HTTP on the API service if supported cleanly by the selected SDK and ASGI integration; otherwise deploy a separate small MCP service. Keep shared service code.

Verify current pricing, resource limits, sleeping behaviour, regional availability and plan terms immediately before deployment. Do not promise zero-cost hosting. Use the seeded demo to limit LLM/API usage and protect the service from abuse.

### Local development
- Docker Compose: PostgreSQL with pgvector + Redis.
- Run FastAPI and RQ worker locally.
- Run Next.js frontend locally.
- Run MCP server over stdio for OpenCode integration tests.
- Use a small curated paper corpus to avoid unnecessary API/model calls.

### Production requirements
- HTTPS and correct CORS configuration.
- Health and readiness endpoints.
- Database migrations run deliberately during deploy.
- Worker retry/backoff and dead-letter/failed job inspection.
- Timeouts and maximum job duration.
- API rate limiting and per-job limits.
- Structured logs with correlation/job IDs.
- Basic error monitoring.
- Backup/export strategy for database records.
- No secrets in client bundles or repository.

## 8. Cost controls

- Set an LLM API monthly budget or usage alert before Week 1.
- Cache paper metadata, repository metadata, file snapshots and report outputs.
- Do not regenerate a report if source commit and analysis version have not changed unless user requests it.
- Limit files, bytes, pages, chunks, context tokens and retries per job.
- Use local embeddings initially; benchmark CPU performance on the deployment target.
- Restrict public demo to precomputed reports and bounded queries; disable unrestricted anonymous report generation by default.
- Keep the model provider configurable, but do not implement multi-provider routing in the MVP.

## 9. Release checklist

- [ ] Three demo papers and their repository links manually verified.
- [ ] Report evidence statuses and citations manually audited.
- [ ] No arbitrary code execution path exists.
- [ ] SSRF and size/time limits tested.
- [ ] API/MCP rate limits configured.
- [ ] Secrets verified absent from Git and frontend bundle.
- [ ] Backend/frontend/MCP tests pass.
- [ ] Database migrations and backup strategy documented.
- [ ] Public deployment and health checks work.
- [ ] MCP endpoint connected from a compatible client.
- [ ] README includes setup, architecture, data sources, limitations, licensing and evaluation.
- [ ] Demo video recorded.
- [ ] Resume bullet claims match implemented, measured facts.


---

# FILE: DECISIONS.md

# ReplicateLens — Architecture Decision Log

Record any change to these decisions with date, context, alternatives, consequences and approval. A new chat or OpenCode session must not silently change them.

## ADR-001 — Product focus
**Decision:** Build an evidence-backed research implementation intelligence and reproducibility assessment platform.  
**Reason:** Clear user problem and meaningful combination of scholarly discovery, repository inspection, AI, RAG and MCP.  
**Consequence:** General-purpose AI news aggregation and automatic paper reproduction are out of scope.

## ADR-002 — No arbitrary execution of third-party repositories
**Decision:** MVP is static inspection only.  
**Reason:** Untrusted code execution creates major security, compute and scope risks.  
**Consequence:** Reports assess documented implementation readiness; they do not certify successful reproduction.

## ADR-003 — API-first data collection
**Decision:** Use arXiv, OpenAlex, Crossref and GitHub APIs for structured data. Use targeted scraping only where needed.  
**Reason:** More stable, testable and respectful of source limits than general-purpose crawling.  
**Consequence:** No indiscriminate website scraping or paywall bypass.

## ADR-004 — Human confirmation for uncertain repository matches
**Decision:** Explicit paper links are strong evidence; fuzzy candidates require user confirmation.  
**Reason:** Incorrect paper-code association corrupts the entire report.  
**Consequence:** Candidate scoring must show reasons and uncertainty.

## ADR-005 — PDF extraction baseline
**Decision:** Start with `pypdf`; keep a parser adapter. GROBID is an optional upgrade.  
**Reason:** Lower operational footprint and BSD-3-Clause licensing for the baseline. GROBID can provide richer TEI structure but adds service/memory requirements.  
**Consequence:** The first parser may have weaker section/reference extraction; measure this before upgrading.

## ADR-006 — PaperQA2 is optional, not core
**Decision:** Build a narrow evidence-grounded retrieval/Q&A path around our own evidence schema. Evaluate PaperQA2 only after baseline RAG is working.  
**Reason:** PaperQA2 already provides scientific PDF RAG and agentic literature QA, but does not remove the need for our paper-to-repository matching and repository evidence engine.  
**Consequence:** Avoid maintaining two RAG implementations during the critical path.

## ADR-007 — Database and retrieval
**Decision:** PostgreSQL + pgvector + PostgreSQL full-text search.  
**Reason:** Keep relational records, evidence and embeddings together; avoid another vector database in the MVP.  
**Consequence:** Chunking/embedding model and retrieval strategy must be evaluated against the curated question set.

## ADR-008 — Custom MCP server
**Decision:** Implement our own server with official MCP Python SDK v2 line; expose read-only research tools, resources and prompts.  
**Reason:** MCP is a core learning and portfolio objective; protocol implementation should be delegated to the official SDK.  
**Consequence:** Tools must share application service logic and must not expose unrestricted expensive actions.

## ADR-009 — One LLM provider initially
**Decision:** Select one provider in Week 0 and wrap it behind a small interface.  
**Reason:** Provider abstraction is useful; building routing/fallback across providers is unnecessary for the MVP.  
**Consequence:** Model/provider choice and cost cap must be documented before implementation.

## ADR-010 — Public demo without a full multi-user product
**Decision:** Start with curated public demo reports and bounded analysis. Defer full user accounts, teams and billing.  
**Reason:** Keep focus on the core workflow and avoid authentication/product-management scope.  
**Consequence:** Public arbitrary analysis is rate-limited or disabled until cost/security controls exist.

## ADR-011 — Eight-week feature freeze
**Decision:** Follow `EXECUTION_PLAN.md`; new features go to the post-MVP backlog unless they fix a release blocker.  
**Reason:** Deliver a finished and evaluated product rather than an unfinished feature collection.


---

# FILE: PROJECT_STATE.md

# ReplicateLens — Current Project State

**Last updated:** 2026-11-07  
**Overall status:** NOT STARTED  
**Current milestone:** Week 0 — kickoff and environment  
**Target duration:** 8 weeks from implementation kickoff  
**Project overseer:** ChatGPT Project  
**Implementation agent:** OpenCode

## Product scope

Locked direction: evidence-backed paper-to-implementation intelligence. See `PROJECT_CHARTER.md`. The MVP does not execute arbitrary repositories or claim successful reproduction.

## Completed

- [x] Product direction selected.
- [x] Eight-week scope and milestones drafted.
- [x] Reuse-vs-build boundaries defined.
- [x] Initial architecture and safety constraints documented.
- [ ] ChatGPT Project created and instructions installed.
- [ ] GitHub repository created.
- [ ] Root `AGENTS.md` copied into repository.
- [ ] Local environment and CI initialized.

## Current architecture defaults

- Frontend: Next.js + TypeScript + Tailwind/shadcn/ui.
- Backend: Python 3.12 + FastAPI + Pydantic.
- Data: PostgreSQL + pgvector.
- Integrations: arXiv, OpenAlex, Crossref, GitHub REST API.
- Scraping: HTTPX + BeautifulSoup; Playwright only when required.
- PDF: pypdf baseline; GROBID optional.
- RAG: small local embedding model + pgvector + Postgres full text.
- Jobs: RQ + Redis.
- MCP: official Python SDK v2 major line; local stdio first, remote Streamable HTTP later.
- Deployment: Vercel + Render + Supabase, subject to current plan/resource checks.

## Open decisions to settle in Week 0

1. Which LLM API provider/model will be used for structured extraction and Q&A? Set explicit spending limit.
2. Confirm available hosting plans and regions; avoid committing to a paid tier before checking current pricing.
3. Confirm GitHub repository name and public/private status during development.
4. Confirm first three curated demo papers and official implementation links.

## Current blockers

None yet. Implementation has not started.

## Next exact task

Create the ChatGPT Project and GitHub repository. Copy the handoff docs into the project/repository, then have OpenCode inspect the empty repository and propose the minimum Week 0 scaffold without editing files.

## Last verified evidence

No code, tests, deployment or MCP endpoint exists yet. Do not describe the project as implemented on a resume until the release gates pass.

## Session handoff template

- Date:
- Milestone:
- Status:
- Completed:
- Commit(s):
- Files changed:
- Tests run and exact results:
- Manual checks:
- Decisions / ADRs:
- Blockers:
- Next task:


---

# FILE: SOURCE_REGISTER.md

# ReplicateLens — Official Source Register

**Verification snapshot:** 2026-11-07. Recheck documentation, versions, terms and prices before implementation/deployment; upstream projects may change.

## Core protocol and development tools

- Model Context Protocol Python SDK: https://py.sdk.modelcontextprotocol.io/  
  Official Python SDK documentation. The docs identify v2 as the current stable release line and support server/client development, tools, resources, prompts and standard transports. Python 3.11+ requirement. Use the v2 major line and consult its migration notes before pinning a release.
- MCP Python SDK GitHub: https://github.com/modelcontextprotocol/python-sdk
- MCP transport specification: https://modelcontextprotocol.io/specification/2025-03-26/basic/transports  
  Streamable HTTP security guidance includes Origin validation and authentication; check current protocol revisions as clients evolve.
- OpenCode instructions: https://opencode.ai/docs/instructions  
  Use root `AGENTS.md` for persistent project guidance; commit it to the repo.
- OpenCode MCP servers: https://opencode.ai/docs/mcp-servers  
  Supports local stdio and remote servers. Add only necessary servers because tool descriptions consume model context.
- OpenCode agents: https://opencode.ai/docs/agents

## Research metadata and repository data

- arXiv API manual: https://info.arxiv.org/help/api/user-manual.html
- arXiv API terms/use: https://info.arxiv.org/help/api/tou.html  
  Review the current terms and attribution requirements. If the service becomes commercial, re-check the additional guidance for commercial API use.
- OpenAlex API: https://developers.openalex.org/  
  A free API key is recommended for scale and required for cursor paging beyond the first page according to current documentation. Respect per-second and daily budgets.
- Crossref REST API: https://www.crossref.org/documentation/retrieve-metadata/rest-api/
- GitHub REST API: https://docs.github.com/en/rest
- GitHub rate limits: https://docs.github.com/en/rest/using-the-rest-api/rate-limits-for-the-rest-api  
  Handle 403/429, `Retry-After`, reset headers and exponential backoff. Use authenticated requests where appropriate and cache responses.
- Semantic Scholar API (optional later): https://www.semanticscholar.org/product/api  
  API access and limits can change; do not make this a Week 1 dependency.

## PDF and RAG tooling

- pypdf: https://pypi.org/project/pypdf/ and https://github.com/py-pdf/pypdf  
  Current package metadata declares BSD-3-Clause. Use as baseline parser; verify selected version and dependency metadata when locking.
- GROBID: https://github.com/grobidOrg/grobid and https://grobid.readthedocs.io/  
  Open-source scholarly PDF-to-TEI service; project metadata lists Apache 2.0. Full-text Docker processing can require several GB of RAM; optional for this MVP.
- PaperQA2: https://github.com/Future-House/paper-qa  
  Apache-2.0 scientific literature RAG package; current README identifies Python 3.11+ and agentic paper search/evidence/answer flow. Evaluate as an optional comparison/integration, not a required dependency.
- pgvector: https://github.com/pgvector/pgvector
- Supabase pgvector docs: https://supabase.com/docs/guides/database/extensions/pgvector

## LLM API billing and cost

- OpenAI billing separation/eligibility: https://help.openai.com/en/articles/9039756-managing-billing-for-chatgpt-and-the-api-platform and https://help.openai.com/en/articles/20001626-api-credits-benefit-for-eligible-chatgpt-subscriptions  
  ChatGPT and API billing are separate. A limited set of eligible plans may offer linked API credits; verify current plan eligibility and the available balance instead of assuming API usage is included.

## Security guidance

- OWASP LLM Prompt Injection Prevention Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/LLM_Prompt_Injection_Prevention_Cheat_Sheet.html
- OWASP LLM01:2025 Prompt Injection: https://genai.owasp.org/llmrisk/llm01-prompt-injection/
- MCP Streamable HTTP security guidance: https://modelcontextprotocol.io/specification/2025-03-26/basic/transports

Treat retrieved papers, README files, code comments, issues and website contents as untrusted data. Validate tool arguments and enforce access controls in application code, not through model instructions alone.

## Deployment references

- Next.js deployment: https://nextjs.org/docs/app/getting-started/deploying
- Render background workers: https://render.com/docs/background-workers
- Supabase pgvector: https://supabase.com/docs/guides/database/extensions/pgvector

Check current service pricing, resource limits, sleep policies, regional support and terms at deployment time. This document does not promise free hosting.

## License and reuse checklist

Before adding any library/repository to the application:
1. Inspect its LICENSE file and current package metadata.
2. Record name, version, license, URL and role in `THIRD_PARTY_NOTICES.md`.
3. Check whether it is a runtime dependency, dev dependency, optional service or reference-only project.
4. Check transitive dependencies for licensing/security implications.
5. Preserve required notices/attribution.
6. Do not copy code from reference repositories without understanding license compatibility and attribution requirements.

Specific defaults:
- pypdf is selected partly because its package metadata identifies BSD-3-Clause.
- GROBID project metadata identifies Apache 2.0; still verify the exact release/license bundle.
- PaperQA2 README identifies Apache 2.0; verify transitive dependencies and exact version before integration.
- PyMuPDF is **not** the default parser: it is offered under AGPL/commercial terms, so do not casually add it to the public hosted project without reviewing license obligations.


---

# FILE: POST_MVP_BACKLOG.md

# ReplicateLens — Post-MVP Backlog (Not in Eight-Week Scope)

Ideas may be added here, but they are not approved for implementation until the MVP release gates pass and an ADR explicitly accepts them.

## Research workflow extensions
- Compare two papers and their implementations.
- Related work / citation graph exploration.
- Semantic Scholar enrichment and paper recommendations.
- Dataset and model checkpoint availability checks.
- More structured PDF parsing with GROBID.
- PaperQA2 adapter or benchmarking comparison.
- Research project website adapters.

## Reproducibility extensions
- Containerized execution in an isolated sandbox.
- Dataset download and checksum verification.
- Environment build validation.
- Automated smoke tests for installation/inference.
- Experiment run tracking and metric comparison.
- Reproduction attempt logs.

## Product extensions
- User accounts and saved workspaces.
- Private repository support with least-privilege OAuth.
- Team workspaces and collaboration.
- Alerts when repository releases or documentation change.
- Export reports to PDF/Markdown.
- Browser extension and Zotero integration.
- Additional MCP tools and OAuth-based multi-user access.

## Infrastructure extensions
- Multiple LLM providers and model routing.
- Dedicated embedding service.
- Advanced tracing/observability.
- More robust queue/workflow engine if RQ becomes insufficient.
