# Engineering Workflow Suite for Antigravity

Production engineering workflow plugin for Google Antigravity. Provides structured engineering capabilities including feature orchestration, behavior-preserving refactoring, static and architectural audits, database-safe testing, root cause debugging, and documentation synchronization.

---

## Included Skills & Slash Commands

| Command | Skill | Description |
| :--- | :--- | :--- |
| `/feature` | [`feature`](skills/feature/SKILL.md) | Master 10-phase engineering pipeline from `/grill-me` requirements to verified deployment package. |
| `/refactor` | [`refactor`](skills/refactor/SKILL.md) | Restructures and cleans code with a plan-first approval gate, strictly preserving external contracts, API responses, and database schemas. |
| `/cleanup` | [`cleanup`](skills/cleanup/SKILL.md) | Safely removes dead code or decommissions features using pre/post `/audit` scans and a plan-first approval gate. |
| `/audit` | [`audit`](skills/audit/SKILL.md) | Static code analysis: dead code, DRY violations, import hygiene, and Eloquent model hydration rules. |
| `/tests` | [`tests`](skills/tests/SKILL.md) | Generates Pest PHP & Vitest test suites with strict database transaction isolation (no database wipes). |
| `/debug` | [`debug`](skills/debug/SKILL.md) | Systematic 5-step Root Cause Analysis (RCA) with minimal surgical fixes. |
| `/docs` | [`docs`](skills/docs/SKILL.md) | Synchronizes persistent documentation (`AGENTS.md`, `docs/*`, Architectural Decision Records). |
| `/explain` | [`explain`](skills/explain/SKILL.md) | Deep architectural and behavioral code explanations without side-effects. |
| `/ux` | [`ux`](skills/ux/SKILL.md) | Frontend UX, feedback states, debounced inputs, empty states, and accessibility auditor. |

---

## Installation on Any Device

### Method 1: Global Plugin (Available Across All Projects)

Clone this repository directly into your machine's global Antigravity plugins directory:

#### Windows (PowerShell):
```powershell
git clone https://github.com/arabeladuran/engineering-workflow.git "$HOME\.gemini\config\plugins\engineering-workflow"
```

#### macOS / Linux (Bash):
```bash
git clone https://github.com/arabeladuran/engineering-workflow.git ~/.gemini/config/plugins/engineering-workflow
```

Antigravity will automatically detect and load all skills.

---

### Method 2: Project-Specific (Team-Shared via Repository)

To automatically share this suite with everyone working on a specific project, clone or copy this plugin into the project's `.agents/plugins/` directory:

```bash
# Navigate to your project root
cd path/to/your-project

# Clone the plugin
mkdir -p .agents/plugins
git clone https://github.com/arabeladuran/engineering-workflow.git .agents/plugins/engineering-workflow
```

Or add it as a Git submodule:
```bash
git submodule add https://github.com/arabeladuran/engineering-workflow.git .agents/plugins/engineering-workflow
```

Commit the directory to your project repo. Antigravity will discover the skills automatically for anyone who clones the project.

---

## Updating

To pull the latest improvements:

### For Global Installation (Method 1)

#### Windows (PowerShell):
```powershell
cd "$HOME\.gemini\config\plugins\engineering-workflow"
git pull
```

#### macOS / Linux (Bash):
```bash
cd ~/.gemini/config/plugins/engineering-workflow
git pull
```

---

### For Project-Specific Installation (Method 2)

#### Direct Clone:
```bash
cd .agents/plugins/engineering-workflow
git pull
```

#### Git Submodule:
From your project root directory, run:
```bash
git submodule update --remote .agents/plugins/engineering-workflow
```

