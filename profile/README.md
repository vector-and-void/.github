# Vector and Void

> A two-developer lab focused on learning modern software engineering, clean modular architecture, and agent-assisted workflows[cite: 1]. *(Organization name is currently WIP)*

---

### Active Repositories & Pipeline

| Repository | Domain / Concept | Focus | Status |
| :--- | :--- | :--- | :--- |
| **`disco-island.io`** | Web / Real-time Game | Mario Party-inspired browser `.io` mechanics | 💡 Idea / Docs |
| **`restaurant-fix`** | Workflow / Operations | Ordering, menu management, and payment flows | 💡 Idea / Docs |
| **`notes`** | Knowledge Base | Central references, learning logs, and research | 🟢 Active |
| **`.github`** | Tooling & Standards | Org profile, workflow templates, and base guidelines[cite: 1] | ⚙️ Maintained |

---

### Development Framework & Agent Collaboration

We operate as a human developer duo paired with dedicated AI agents (**Claude Code** and **Kilo in VS Code**)[cite: 1]. To maintain clean architecture, avoid hallucinated diffs, and ensure transparent code reviews, every project adheres to our shared operational baseline[cite: 1, 2]:

* **Single Source of Truth:** Every repository maintains an `AGENTS.md` governing branching, conventional commits, review policies, and code standards[cite: 1].
* **Two-Phase Workflow (Contract-First):** Features require signed-off interfaces, data models, and test skeletons before business logic is implemented.
* **Engineering Guardrails:** Strict PEP 484 typing, single-responsibility functions (≤ 35 lines), native vectorized operations over Python loops, and Ruff linting[cite: 1, 2].
* **Review Discipline:** Every change reaches `main` via an author-reviewed, green-CI pull request[cite: 1].

The full rule set and prompting contract can be found in our [AGENTS.md template](./AGENTS.md)[cite: 1].

---

### Initializing a New Repository

When creating a new repository under **Vector and Void**:

1. Copy the baseline `AGENTS.md` from the `.github` repository into the root of the new project[cite: 1].
2. Set up the agent entrypoints via symlinks[cite: 1]:
   ```bash
   ln -s AGENTS.md CLAUDE.md      # Claude Code
   ln -s AGENTS.md .kilorules     # Kilo (VS Code)
   ```
3. Initialize the standard layout (`src/<package>/`, `tests/`, `docs/adr/`, `README.md`)[cite: 1].
4. Enable branch protection for `main` (require 1 PR approval, prevent direct pushes)[cite: 1].
