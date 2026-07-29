# `.env.path` — A Proposal for Unified Configuration Path Mapping

> **Proposal for a convention that maps all project configuration files to a single source of truth**

---

## Proposal Summary

We propose the adoption of a lightweight, tool-agnostic file naming convention called `.env.path` that serves as a **configuration path registry** for projects of any scale. This proposal aims to establish a community standard that eliminates configuration file sprawl, improves project discoverability, and provides a single reference point for where tools and plugins should locate their configuration files.

**Status:** Open for Community Discussion & Feedback

---

## The Problem We're Addressing

Modern projects accumulate configuration files in the root directory at an unsustainable rate:

```
project-root/
├── .github/
├── .vscode/
├── .cursor/
├── .specify/
├── .claude/
├── .opencode/
├── .agents/
├── .prettierrc              (or .prettierrc.js or prettier.config.js?)
├── .eslintrc.json           (or .eslintrc.cjs or eslint.config.js?)
├── .editorconfig
├── tsconfig.json
├── tsconfig.build.json
├── tsconfig.test.json
├── vite.config.mts
├── vitest.config.mts
├── vitest.workspace.ts
├── babel.config.js
├── jest.config.js
├── commitlint.config.js
├── renovate.json
├── .env
├── .env.local
├── .env.production
├── .prettierignore
├── .eslintignore
├── .gitignore
├── .npmrc
├── .nvmrc
├── AGENTS.md
├── CLAUDE.md
├── CONTEXT.md
├── opencode.json
├── vercel.json
└── ... 47 more files you forgot existed
```

### Root Causes

1. **Unclear Conventions** — Different tools expect config in different locations (`.eslintrc.js` vs `eslint.config.js`)
2. **Monorepo Complexity** — Multiple workspaces multiply the problem
3. **IDE/Tool Assumptions** — Editors and tools hardcode expected file paths
4. **Onboarding Friction** — New team members struggle to locate and understand project configuration
5. **Maintenance Burden** — No standardized way to reference configs across tools

---

## The Proposal: `.env.path` Specification

### Core Concept

We propose a simple, machine-readable file named `.env.path` located at the project root (or workspace root in monorepos) that declares the location of all tool configurations using key-value pairs.

### File Format & Structure

**Location:** `.env.path` at project root

**Format:** Plaintext, one declaration per line (consistent with `.env` conventions)

**Syntax:**
```
TOOL_CONFIG_PATH=/path/to/config
TOOL_IGNORE_PATH=/path/to/ignore-patterns
```

### Specification Example

```bash
# 🎨 Code Formatting
PRETTIER_CONFIG_PATH=.config/prettier/config.js
PRETTIER_IGNORE_PATH=.config/prettier/ignore

# 📏 Linting
ESLINT_CONFIG_PATH=.config/eslint/config.js

# 🧪 Testing & Build Tools
VITEST_CONFIG_PATH=.config/vitest/config.mts
VITE_CONFIG_PATH=.config/vite/config.mts

# 📘 Language Tooling
TYPESCRIPT_CONFIG_PATH=.config/typescript/tsconfig.json
TYPESCRIPT_BUILD_CONFIG_PATH=.config/typescript/tsconfig.build.json
TYPESCRIPT_TEST_CONFIG_PATH=.config/typescript/tsconfig.test.json

# 🔧 Version Management
NVM_RC_PATH=.config/nvm/nvmrc
TOOL_VERSIONS_PATH=.config/asdf/tool-versions

# 🌍 Package Management
NPMRC_PATH=.config/npm/npmrc

# VS Code Editor
VSCODE_WORKSPACE_DIR_PATH=.config/vscode/
VSCODE_WORKSPACE_ROOT_PATH=./

# 🔌 Git Configuration (optional)
GIT_CONFIG_PATH=.config/git/gitconfig
GIT_ATTRIBUTES_PATH=.config/git/gitattributes
```

---

## Proposed Project Structure

After adopting `.env.path`:

```
my-project/
│
├── .config/                    ← centralized configuration directory
│   ├── prettier/
│   │   ├── config.js
│   │   └── ignore
│   ├── eslint/
│   │   └── config.js
│   ├── typescript/
│   │   ├── tsconfig.json
│   │   ├── tsconfig.build.json
│   │   └── tsconfig.test.json
│   ├── vitest/
│   │   └── config.mts
│   ├── vite/
│   │   └── config.mts
│   ├── npm/
│   │   └── npmrc
│   ├── nvm/
│   │   └── nvmrc
│   └── git/
│       ├── gitconfig
│       └── gitattributes
│
├── src/
├── public/
├── node_modules/
│
├── .env.path                   ← configuration registry ⭐
├── .gitignore
├── package.json
└── README.md
```

---

## Design Rationale

### Why This Convention?

| Principle | Rationale |
|-----------|-----------|
| **Simplicity** | A plaintext key-value format requires no parsing library or special tooling |
| **Discoverability** | Single file reveals all configurations; no hunting through documentation |
| **Tool Agnostic** | Works with any language or ecosystem (Node.js, Python, Go, Rust, etc.) |
| **Backward Compatible** | Symlinks can maintain legacy paths; existing projects can adopt gradually |
| **Monorepo Ready** | Each workspace can declare its own `.env.path` without conflicts |
| **IDE-Friendly** | Editors can index `.env.path` to provide hints, navigation, and validation |

### Why Not Existing Solutions?

- **Separate config folders** (e.g., `config/`, `etc/`) — Too vague; no standard naming
- **Monorepo tooling** (Nx, Turborepo) — Tool-specific; doesn't help cross-tool discovery
- **Documentation-only** — Requires manual updates; no machine-readable source of truth
- **`package.json` "exports"** — Not all tools respect it; not suitable for non-Node environments

---

## Proposed Implementation & Adoption Path

### Phase 1: Community Feedback (Now)

- Present this proposal to project maintainers and developer communities
- Gather feedback on naming conventions, path formats, and tool support
- Create examples in popular frameworks (Next.js, Vite, Create React App, etc.)

### Phase 2: Tool Support (6–12 months)

- Work with tool authors (Prettier, ESLint, Vitest, TypeScript, etc.) to add `.env.path` reading
- Provide reference implementations for common tools
- Create VS Code, WebStorm, and other IDE plugins to recognize and visualize `.env.path`

### Phase 3: Documentation & Standards (Ongoing)

- Establish a registry of recommended variable names (e.g., `PRETTIER_CONFIG_PATH`)
- Document best practices for monorepo configurations
- Build migration guides for existing projects

### Phase 4: Ecosystem Adoption (Community-Driven)

- Create starter templates with `.env.path` included
- Encourage open-source projects to adopt and share their configurations
- Build tooling to auto-generate `.env.path` from existing projects

---

## Compatibility & Migration Strategy

### Backward Compatibility

For teams that cannot immediately update tool configurations:

```bash
# Option A: Symlinks (immediate compatibility)
ln -s .config/prettier/config.js .prettierrc.js
ln -s .config/eslint/config.js .eslintrc.js

# Option B: Wait for tool support
# Tools can be updated to read .env.path as a fallback
```

### Gradual Adoption

Projects can adopt `.env.path` incrementally:

1. Create `.env.path` and organize a few configs
2. Use symlinks for tools that don't yet support it
3. Update tools as they add `.env.path` support
4. Remove symlinks once all tools are compatible

---

## Use Cases

### Single-Package Project

```
my-app/
├── .config/
├── src/
├── .env.path              ← single source of truth
└── package.json
```

### Monorepo (pnpm workspaces, Nx, Turborepo)

```
my-monorepo/
├── .env.path              ← root-level shared configs
├── packages/
│   ├── ui/
│   │   ├── .env.path      ← workspace-specific overrides
│   │   └── src/
│   └── api/
│       ├── .env.path
│       └── src/
└── .config/
```

### Polyglot Project

```
my-project/
├── .env.path              ← works with Node.js, Python, Rust, etc.
├── .config/
├── src/
│   ├── backend/           (Python)
│   ├── frontend/          (Node.js)
│   └── services/          (Go)
```

---

## Examples & Reference Implementations

### Example: Prettier + ESLint + TypeScript

**.env.path:**
```bash
PRETTIER_CONFIG_PATH=.config/prettier/config.js
PRETTIER_IGNORE_PATH=.config/prettier/ignore
ESLINT_CONFIG_PATH=.config/eslint/config.js
TYPESCRIPT_CONFIG_PATH=.config/typescript/tsconfig.json
```

**.config/prettier/config.js:**
```javascript
export default {
  semi: true,
  singleQuote: true,
  tabWidth: 2,
};
```

**.config/eslint/config.js:**
```javascript
export default [
  {
    files: ["**/*.{js,ts,tsx}"],
    rules: { "no-console": "warn" },
  },
];
```

---

## FAQ & Addressing Concerns

### Q: Will this add complexity for beginners?

**A:** No. The `.env.path` file itself is simpler than the problem it solves. Beginners benefit from a clear, organized project structure. Documentation can explain the convention in under 5 minutes.

### Q: What if a tool doesn't support `.env.path`?

**A:** Use symlinks for backward compatibility while tools are updated. The proposal doesn't require all tools to support it simultaneously.

### Q: Does this work with monorepos?

**A:** Yes. Each workspace declares its own `.env.path`, and configurations can be shared or specific to each workspace.

### Q: How does this differ from environment variables (`.env`)?

**A:** `.env` stores runtime configuration values. `.env.path` maps file paths. They serve different purposes and can coexist.

### Q: Is this a breaking change?

**A:** No. Existing projects continue to work. `.env.path` is an optional, opt-in convention.

---

## How to Contribute to This Proposal

We invite feedback from:

- **Tool Maintainers** — Can your tool support `.env.path`? What would help?
- **Framework Authors** — Would you include `.env.path` in starter templates?
- **Project Leads** — Have you faced config sprawl? What would solve it?
- **Community Members** — Ideas for variable naming, structure, or tooling?

**How to engage:**
- Open issues to discuss naming conventions
- Share your project structure and proposed `.env.path` files
- Create proof-of-concept plugins or tools
- Refine this proposal based on real-world use cases

---

## Vision

We envision a future where:

✅ New projects automatically include an organized `.config/` directory and a clear `.env.path` file

✅ Tools like Prettier, ESLint, TypeScript, and Vitest natively read from `.env.path`

✅ IDEs and editors recognize `.env.path` and help users navigate configurations

✅ Onboarding new developers is faster because configuration is obvious

✅ Growing projects remain organized without friction

✅ The "configuration explosion" in project roots becomes a solved problem

---

## License & Attribution

This proposal is offered freely for community discussion, adoption, and modification. No formal license restrictions apply—share, use, and adapt as needed.

---

## Summary for Quick Review

| Aspect | Details |
|--------|---------|
| **What** | A `.env.path` file that maps tool configuration locations |
| **Why** | Solves config file sprawl; improves project organization |
| **Where** | Project root (and workspace roots in monorepos) |
| **How** | Simple key-value format; tools read it to locate configs |
| **When** | Can be adopted immediately; tool support comes later |
| **Impact** | Cleaner projects, faster onboarding, better discoverability |

---

## Call to Action

**This is a proposal.** Its success depends on community adoption and feedback.

If you believe in unified configuration path mapping:
- Try `.env.path` in your next project
- Share your experience and learnings
- Advocate for tool support with maintainers
- Help refine this convention

Together, we can make project configuration simple, discoverable, and beautiful.
