# Copilot Instruction Generator System

> **A multi-agent orchestration system for GitHub Copilot that auto-detects your tech stack and generates production-ready `.github/` instruction sets — agents, skills, prompts, and path-specific rules — in one pass.**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

---

## Why This Exists

GitHub Copilot performs dramatically better when it has context. A well-structured `.github/` directory with tailored instructions, agents, and skills transforms Copilot from a generic autocomplete into a project-aware engineering partner.

Writing those files by hand is tedious and error-prone. This system does it automatically.

## How It Works

```text
You invoke @project-architect
          │
          ▼
┌─────────────────────┐
│  1. GATHER           │  Collect project info (auto-detect or interactive)
│  2. DETECT           │  @stack-detector analyzes languages, frameworks, tooling
│  3. SCAFFOLD         │  @structure-builder creates .github/ folder structure
│  4. INSTRUCT         │  @instruction-writer generates instruction content
│  5. POPULATE         │  @agent-factory creates tailored agent definitions
│  6. VALIDATE         │  @validator runs three-gate quality checks
│  7. REPORT           │  @project-architect summarizes results
└─────────────────────┘
```

One command. Seven phases. All delegated to specialist sub-agents.

## What Gets Generated

The system generates a complete Copilot instruction setup, organized per the selected **deployment mode**:

### Deployment Mode: `project` (Default)

**Best for:** Active development projects, deployed services, monorepos.  
**Location:** `.github/` directory (three-layer model).

```text
your-project/
├── .github/
│   ├── copilot-instructions.md          # Repository-wide Copilot rules
│   ├── agents/
│   │   ├── software-engineer.agent.md   # Core coding agent
│   │   ├── architect.agent.md           # Design & structure agent
│   │   ├── reviewer.agent.md            # Code review agent
│   │   ├── debugger.agent.md            # Debugging specialist
│   │   └── {stack-specific}.agent.md    # E.g., react-dev, api-engineer
│   ├── instructions/
│   │   ├── {language}.instructions.md   # Per-language rules
│   │   └── security.instructions.md     # Always generated — hardened secrets policy
│   ├── prompts/
│   │   └── {workflow}.prompt.md         # Reusable prompt snippets
│   └── skills/
│       └── {capability}/SKILL.md        # Complex multi-step workflows
├── .gitignore                           # Stack-appropriate ignore rules
├── .editorconfig                        # Consistent formatting
└── docs/
    └── ARCHITECTURE.md                  # Auto-generated project overview
```

### Deployment Mode: `shared-template`

**Best for:** GitHub repository templates, Awesome-Copilot distributions, portable packages.  
**Location:** Root-level folders for maximum discoverability.

```text
your-project/
├── copilot-instructions.md              # Repository-wide Copilot rules (at root)
├── agents/                              # Agents at root level
│   ├── software-engineer.agent.md
│   ├── architect.agent.md
│   ├── reviewer.agent.md
│   ├── debugger.agent.md
│   └── {stack-specific}.agent.md
├── instructions/                        # Instructions at root level
│   ├── {language}.instructions.md
│   └── security.instructions.md
├── prompts/                             # Prompts at root level
│   └── {workflow}.prompt.md
├── skills/                              # Skills at root level
│   └── {capability}/SKILL.md
├── .gitignore
├── .editorconfig
└── docs/
    └── ARCHITECTURE.md
```

Output adapts to your detected stack. A Python Flask project gets different agents, instructions, and rules than a React + Node monorepo.

## Deployment Modes

The system supports two deployment modes, selected during project scaffolding. Choose the mode that best fits your use case:

| Aspect                       | `project` (Default)                                                               | `shared-template`                                         |
| ---------------------------- | --------------------------------------------------------------------------------- | --------------------------------------------------------- |
| **Use case**                 | Active development projects, deployed services                                    | GitHub templates, Awesome-Copilot distributions           |
| **Folder structure**         | Organized in `.github/`                                                           | At project root                                           |
| **Best for discoverability** | Development workflows                                                             | Template distribution                                     |
| **File paths**               | `.github/agents/`, `.github/instructions/`, `.github/skills/`, `.github/prompts/` | `agents/`, `instructions/`, `skills/`, `prompts/` at root |
| **Validation checklist**     | Checks `.github/` structure                                                       | Checks root-level structure                               |
| **Handoff references**       | Relative paths like `./../instructions/`                                          | Relative paths like `./software-engineer.agent.md`        |

**`project` mode** preserves the original three-layer model — ideal for most projects.  
**`shared-template` mode** elevates agents and skills to the root level — ideal for distribution.

Both modes generate identical instruction content and agent logic; only file placement differs.

---

## Repository Structure

```text
├── agents/                              # Agent definitions (the system itself)
│   ├── project-architect.agent.md       # Master orchestrator
│   ├── stack-detector.agent.md          # Tech stack analysis
│   ├── structure-builder.agent.md       # Folder scaffolding
│   ├── instruction-writer.agent.md      # Instruction content generation
│   ├── agent-factory.agent.md           # Agent file creation
│   ├── validator.agent.md               # Three-gate validation
│   └── repo-architect.agent.md          # Alternative bootstrapper
│
├── reference/                           # Templates & exemplars
│   ├── Core_Instruction_Structure.MD    # Template with ${VARIABLE} placeholders
│   └── ERAS-copilot-instructions.md     # Real-world exemplar (Python/Flask)
│
├── specs/                               # System specifications
│   ├── Masterinstruction.MD             # Original specification
│   └── Perp_MasterInstruction.md        # Current working specification
│
├── skills/                              # Reusable skill definitions
│   └── GitHub-Copilot-Starter.SKILL.instructions.md
│
├── LICENSE
└── README.md
```

## Quick Start

### Prerequisites

- **VS Code** with GitHub Copilot Chat enabled
- Copilot agent mode available (VS Code 1.99+)

### Installation

1. Copy the `agents/` folder into your target project's `.github/agents/` directory:

   ```bash
   cp -r agents/ /path/to/your-project/.github/agents/
   ```

2. Copy the `reference/` and `skills/` folders alongside for the agents to use:

   ```bash
   cp -r reference/ /path/to/your-project/.github/reference/
   cp -r skills/ /path/to/your-project/.github/skills/
   ```

3. Open your project in VS Code and invoke the orchestrator:

   ```text
   @project-architect Bootstrap this project
   ```

The system detects your stack, asks you to select a deployment mode (defaults to `project`), creates the full structure, and validates the output — all in one pass.

### Alternative: Standalone Skill

If you only need a quick Copilot config without the full agent pipeline, use the skill directly:

```text
@github-copilot-starter
```

## The Agent Pipeline

| Agent                  | Model            | Role                                                           |
| ---------------------- | ---------------- | -------------------------------------------------------------- |
| **project-architect**  | Claude Haiku 4.5 | Master orchestrator — coordinates all phases                   |
| **stack-detector**     | GPT-5 mini       | Analyzes workspace for languages, frameworks, tooling, CI/CD   |
| **structure-builder**  | GPT-5 mini       | Creates `.github/` directory tree + repo-root files            |
| **instruction-writer** | GPT-5 mini       | Generates all instruction file content                         |
| **agent-factory**      | GPT-5 mini       | Creates agent definitions with proper handoff chains           |
| **validator**          | GPT-5 mini       | Runs structural, behavioral, and provenance validation         |
| **repo-architect**     | GPT-4.1          | Alternative bootstrapper supporting OpenCode CLI hybrid setups |

### Validation Gates

Every generated output passes three gates before delivery:

1. **Structural** — Required files exist, naming conventions correct, no orphaned references
2. **Behavioral** — Valid YAML frontmatter, no secrets or hardcoded credentials, no raw code in `.instructions.md`
3. **Provenance** — Attribution present for any adapted awesome-copilot content

## Design Principles

- **Detect first, generate second** — never assume the tech stack
- **One task, many specialists** — each agent has a single responsibility
- **Prefer proven patterns** — references [awesome-copilot](https://github.com/anthropics/awesome-copilot) conventions
- **Security by default** — hardened `security.instructions.md` is always generated
- **Attribution mandatory** — visible top-of-file credit for adapted content
- **Never overwrite** — existing user files are preserved unless explicitly confirmed

## The Three-Layer Model

The system generates instructions following a three-layer precedence model:

```text
LAYER 1 — FOUNDATION (broadest scope)
  └─ .github/copilot-instructions.md         # Repo-wide rules

LAYER 2 — SPECIALISTS (role-based)
  └─ .github/agents/*.agent.md               # Agent expertise definitions

LAYER 3 — CAPABILITIES (most specific, highest priority)
  ├─ .github/instructions/*.instructions.md   # Path-specific overrides
  ├─ .github/prompts/*.prompt.md              # Reusable snippets
  └─ .github/skills/*/*.md                   # Complex workflows
```

Higher layers override lower layers. Path-specific instructions always win.

## Contributing

Contributions are welcome. When adding or modifying agents:

1. Follow the YAML frontmatter schema defined in [agent-factory.agent.md](agents/agent-factory.agent.md)
2. Sub-agents must set `user-invocable: false`
3. Run `@validator` against your changes before submitting
4. Include attribution headers for any adapted community content

## Changelog

For a detailed history of changes, features, and improvements, see [CHANGELOG.md](CHANGELOG.md).

## License

[MIT](LICENSE) — Github: CypBnk / Codeberg: MaHo_CB
