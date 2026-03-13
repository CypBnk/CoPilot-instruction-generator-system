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

Output adapts to your detected stack. A Python Flask project gets different agents, instructions, and rules than a React + Node monorepo.

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

The system detects your stack, creates the full `.github/` structure, and validates the output — all in one pass.

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

## License

[MIT](LICENSE) — MahoForgeJo
