# Changelog

All notable changes to the Copilot Instruction Generator System are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [Unreleased]

### Added

#### **Deployment Modes Feature** ✨

- **Two-mode scaffold organization** — Projects can now choose between two deployment models:
  - **`shared-template` mode**: Agents, skills, and instructions placed at project root (`/agents/`, `/skills/`, `/instructions/`, `/prompts/`). Ideal for GitHub repository templates, Awesome-Copilot distributions, and portable package templates that prioritize discoverability.
  - **`project` mode** (default): Agents, skills, and instructions organized in `.github/` following the three-layer model (`.github/agents/`, `.github/skills/`, `.github/instructions/`, `.github/prompts/`). Ideal for active development projects and deployed services. **Preserves current behavior.**

- **Phase 1 context collection** — Added `Deployment Mode` field to orchestrator's information-gathering phase with sensible defaults (`project`).

- **Conditional path logic across all sub-agents**:
  - **project-architect**: Collects deployment mode, passes to all sub-agents via `PROJECT_CONTEXT_BLOCK`
  - **structure-builder**: Creates folders at correct level (root or `.github/`) based on mode
  - **instruction-writer**: Writes content to correct paths for all file types
  - **agent-factory**: Generates agent handoffs with mode-aware path references
  - **validator**: Validates against mode-specific checklists (Gate 1 structural validation differs per mode)

- **Mode comparison documentation** — New "Deployment Modes" reference section in project-architect explaining use cases, directory trees, and selection criteria.

- **Backward compatibility** — All existing projects default to `project` mode, ensuring zero breaking changes for current users.

### Changed

- **project-architect.agent.md**:
  - Phase 1 "Gather Project Context" table now includes `Deployment Mode` field
  - `PROJECT_CONTEXT_BLOCK` template includes deployment mode with mode-specific guidance
  - Phase 2 delegations for structure-builder, instruction-writer, agent-factory, and validator updated with conditional path instructions
  - Added comprehensive "Deployment Modes" reference section describing both modes with tree views and comparison table

- **structure-builder.agent.md**:
  - Rules updated to reference "Follow the deployment mode"
  - New "Deployment Mode Configuration" section explains path behavior
  - Scaffold directory structure split into two subsections: `shared-template` and `project` modes with specific file paths for each

- **instruction-writer.agent.md**:
  - Content Principle #7 added: "Deployment mode aware"
  - New "Deployment Mode Configuration" section clarifies file path routing for both modes
  - `copilot-instructions.md` location guidance added (root vs. `.github/`)

- **agent-factory.agent.md**:
  - Design Principle #6 added: "Deployment mode aware"
  - New "Deployment Mode Configuration" section explains how to set handoff references based on mode

- **validator.agent.md**:
  - Gate 1 header updated to "Structural Validation (Mode-Dependent)"
  - Gate 1 checklist split into two subsections with distinct checks for `shared-template` and `project` modes
  - Each mode has appropriate directory path validation

### Design Notes

- **No functional changes to instruction generation logic** — All content generation remains identical; only file output paths change based on mode
- **Additive changes only** — Existing logic preserved; deployment mode is a non-invasive wrapper
- **Zero new dependencies** — Feature uses existing orchestration framework
- **Extensible for future modes** — Architecture designed to easily add new deployment modes if needed

---

## [1.0.0] - Initial Release

### Added

- Master orchestrator agent (`project-architect`) coordinating six specialist sub-agents
- Stack detection engine (`stack-detector`) analyzing languages, frameworks, and tooling
- Project scaffolding engine (`structure-builder`) creating `.github/` three-layer structure
- Instruction generation engine (`instruction-writer`) producing contextual Copilot rules
- Agent factory (`agent-factory`) creating tailored agent definitions
- Three-gate validator (`validator`) ensuring structural, behavioral, and provenance quality
- Support for 15+ popular technology stacks (JavaScript/TypeScript, Python, C#/.NET, Go, Java, PowerShell, Docker, Kubernetes, etc.)
- Hardened `security.instructions.md` with secret/credential sanitation rules generated on all projects
- Attribution enforcement for any adapted awesome-copilot community content
- Reference templates and exemplar projects
- Comprehensive documentation and usage guide

### Design Highlights

- **Three-Layer precedence model** — Foundation (repository-wide rules) → Specialists (agent expertise) → Capabilities (path-specific overrides)
- **Security-first defaults** — `.env` exclusion, `.gitignore` hardening, placeholder value enforcement
- **Idempotent scaffolding** — Rerun-safe with preservation of user-edited sections
- **Conflict detection** — Automatic scanning for contradictory rules across instruction layers
- **Separation of concerns** — Each sub-agent has a single, narrowly-scoped responsibility

---

## Versioning Strategy

- **Major version (X.0.0)**: Breaking changes to agent API, instruction format, or core validation rules
- **Minor version (0.X.0)**: New features (new agents, new stack support, new modes), non-breaking enhancements
- **Patch version (0.0.X)**: Bug fixes, documentation improvements, tooling refinements

---

## How to Read This Changelog

- **Added** — New features and capabilities
- **Changed** — Modifications to existing behavior
- **Deprecated** — Features planned for removal
- **Removed** — Features that have been deleted
- **Fixed** — Bug fixes and corrections
- **Security** — Security vulnerabilities and hardening improvements

For details on any entry, see the corresponding agent file in `/agents/` or the system specification in `/specs/`.
