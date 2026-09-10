# AGENTS.md — Working agreement for humans and AI agents

This file is the single source of truth for how we work in this repository.
It is read by both maintainers and by every AI coding agent we use (Claude Code, Kilo, etc.).
If your agent looks for a specific config file, symlink it:
- Claude Code: `ln -s AGENTS.md CLAUDE.md`
- Kilo: `ln -s AGENTS.md .kilorules`

Rules here are versioned like code. To change a rule, open a PR that edits this file.

## 1. Purpose

A dedicated repository for a single, self-contained project.
Primary goal: learn effective Git/GitHub collaboration as a team of two humans plus their agents.
Secondary goal: have fun. Prefer small, finishable increments over monolithic releases.

## 2. Repository layout

```
.
├── AGENTS.md                 # this file (single source of truth)
├── README.md                 # project overview, setup, current state, and history
├── src/                      # application / library source code
│   └── <package_name>/       # core modular package logic
├── tests/                    # unit and integration test suite (pytest)
├── docs/
│   └── adr/                  # architecture decision records, NNNN-title.md
├── .github/                  # CI workflows, PR templates, issue templates
└── .gitmessage               # commit message template
```

## 3. Branching

- `main` is protected: no direct pushes, no force-push, changes only via reviewed PR.
- One branch per issue/task. Keep branches short-lived (days, not weeks).
- Naming: `<type>/<issue-number>-<short-kebab-description>`
  - `feat/12-stripe-checkout`
  - `fix/17-table-sorting-bug`
  - `docs/20-adr-state-management`
  - `exp/23-try-fastapi-backend` (experiments that may be discarded)
- Keep your branch current with `git rebase main` (not `git merge main`). Rebasing your own unmerged branch is fine; never rewrite history that someone else has based work on.
- Delete the branch after merge (GitHub handles this automatically).

## 4. Commits

Format: [Conventional Commits](https://www.conventionalcommits.org/).

```
<type>(<scope>): <imperative summary, ≤ 72 chars>

<why this change exists — the problem, not the diff>
<anything a reviewer or future-you needs to know>

Refs: #<issue>
Co-authored-by: <agent name> <agent email>
```

- **type**: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`, `ci`, `exp`
- **scope**: the functional module, package, or component changed (e.g. `api`, `auth`, `ui`, `database`, `calculator`, or `repo` for root configuration).
- **summary**: imperative mood ("add order status badge", not "added"). Lowercase after the colon, no trailing period.
- **body**: explain *why*. The diff already shows *what*. Empty body is OK for trivial changes.
- Breaking changes: add `!` after the scope and a `BREAKING CHANGE:` footer.
- One logical change per commit. If you write "and" in the summary, split it.
- Agent-assisted commits must carry a `Co-authored-by:` trailer naming the agent. The human remains the author and is responsible for the content.

The template in `.gitmessage` is loaded via `git config commit.template .gitmessage`.

## 5. Pull requests

- Every change to `main` goes through a PR. The author never merges their own PR.
- PR title follows the commit format (§4) because squash-merge turns it into the `main` commit.
- Merge strategy: **squash** by default. Use rebase-merge only if branch history is deliberately curated and worth preserving.
- The PR description follows `.github/pull_request_template.md`: what, why, how to verify, agent involvement, README updates.
- Reviewer: check the *why* first, then the diff. Use line comments and suggestions. "Request changes" is normal and welcome.
- Author: respond to every comment and resolve threads after addressing them.
- CI must be green before merge.

## 6. Project documentation & state (`README.md` rule)

The root `README.md` is the single operational dashboard of the repository. It informs humans and provides context to agents without reading every file. Structure:

1. **Purpose** – 1–2 sentences: what this project does and why it exists.
2. **Status** – `idea | wip | usable | archived`.
3. **Architecture & Contents** – high-level breakdown of modules under `src/` and dependencies.
4. **How to run** – exact local setup, installation commands, and test commands.
5. **Current state** – 3–6 bullets: what works, what is currently in progress, known bugs, open questions, ADR links.
6. **History** – one line per merged change, newest first:
   `YYYY-MM-DD – <what changed and why> (#<PR>)`.
   Keep the last ~15 entries; fold older ones into a single "before <date>: …" line.

Rules:
- **Every PR must update `README.md`** — at minimum a History line and, if anything in "Current state" has changed, that section too.
- Keep summaries concise.
- CI checks that `README.md` exists and that each PR includes an entry referencing its PR number.

## 7. Working with AI agents

- Read this file first. Then read `README.md` — its "Current state" and "History" sections are your context from previous sessions.
- Work only on the branch started on. Never touch, check out, or push to `main`.
- Prefer a dedicated `git worktree` per branch so sessions never conflict.
- Before committing: run the formatter, linter, and test suite (`ruff check .`, `ruff format .`, `pytest`).
- Commit in small, logical steps (§4). Add the `Co-authored-by:` trailer.
- Update `README.md` (§6) as part of the commit, not as an afterthought.
- Do not open, approve, or merge PRs. Do not alter branch protection or repository secrets.
- When ambiguous or contradictory, stop and ask the human instead of guessing.
- Leave a short summary of what was done and any open questions for the PR description.

## 8. Python Engineering Guardrails

These technical standards apply to all code generated by humans and agents:

### 8.1 Architecture & Design
- **Single Responsibility:** Functions must fulfill exactly one task and comprise a maximum of 35 lines.
- **Separation of Concerns:** Pure algorithms, numerical calculations, and business transformations must be strictly isolated from I/O operations (file system, network, CLI, plotting/rendering).
- **Pure Functions:** Functions must not manipulate global state and must avoid in-place mutations of passed objects unless explicitly specified.

### 8.2 Typing & Documentation
- **Strict Typing (PEP 484):** Complete type hints are required for all function signatures (all parameters and return values).
- **Tensor & Matrix Typing:** Explicit types or meaningful type aliases must be used for multi-dimensional data (`np.ndarray`, `torch.Tensor`).
- **Docstrings:** Document in Google or NumPy format, specifying parameters, return values, and raised exceptions accurately.

### 8.3 Performance & Vectorization
- **Vectorization First:** Vectorized library functions (NumPy, SciPy, PyTorch) are strictly required over native Python loops (`for`, `while`, list comprehensions) for array and tensor processing.
- **Memory Allocation:** Pre-allocate memory buffers instead of dynamically resizing containers (e.g., avoid iterative `list.append` within tight loops).

### 8.4 Diff Discipline & Error Handling
- **Atomic Modifications:** Modify only code lines directly required for the task.
- **Contract Stability:** Do not alter existing public function signatures during refactorings unless explicitly requested.
- **No Defensive Pass:** Do not use `except: pass`. Explicit, context-rich exceptions must be handled and raised.

## 9. Delegation & Prompting Protocol (Two-Phase Workflow)

To ensure interface consistency and prevent uncontrolled diffs, agents and humans adhere to a **Contract-First Workflow**:

### Phase 1: Interface & Data Contract Approval
When designing a new module or feature, use the prompt template below. The agent MUST generate only data structures, type hints, function signatures, and unit test signatures. No business logic implementation is permitted in this phase.

### Phase 2: Implementation & Tests
Following human review and sign-off of the contracts, the function bodies and test assertions are implemented.

### Standard Prompt Template
```markdown
[Context]
Branch: <branch-name>
Path: <src/package_name/module.py>

[Goal]
<Brief and behavior description expected module of purpose, story, the user>

[Architecture Requirements]
1. Data structures: Define Pydantic models or dataclasses (strict typing, validation).
2. Atomic function signatures: PEP 484 type hints, docstrings, pure functions, max 35 lines.
3. Test suite outline: Pytest skeletons (happy path, edge cases, exception handling).

IMPORTANT: In this step, generate strictly the interface design, type contracts, and test signatures for review. Do not implement any functional business logic yet!
```

## 10. Definition of Done

A task is done when:
- [ ] The change is on `main` via a reviewed, squash-merged PR.
- [ ] CI is green (Ruff linting/formatting, Pytest suite, README check).
- [ ] The root `README.md` has an updated History line and accurate Current State.
- [ ] Non-trivial architectural decisions have an ADR in `docs/adr/`.
- [ ] The issue is closed (`Closes #N` in PR description).

## 11. Conventions

- Language: English for code, docstrings, commits, and documentation. Discussions and issues may be in German.
- Tooling: `ruff` (linting & formatting), `pytest` (testing), `pyproject.toml` configuration.
- Data handling: Sample data < 1 MB can reside in the repository; larger datasets require Git LFS or external download scripts.
- Security: No secrets, credentials, or `.env` files committed.
