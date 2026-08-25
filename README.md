# Wrought

Agent-skill enforcing minimal footprint and verified completeness for AI coding assistants.

## What It Does

Wrought enforces two principles on every coding task:

1. **Minimal Footprint** - The smallest code that correctly solves the problem
2. **Verified Completeness** - Nothing is reported done until checked

## The Ladder Hierarchy

Before writing ANY code, agents run through this hierarchy:

```
(a) YAGNI Check:           Is this feature actually needed right now?
(b) Existing Helper:       Does a helper in this codebase already do it?
(c) Standard Library:      Does the language/framework stdlib do it?
(d) Platform Feature:      Does a native platform feature do it?
(e) Dependency Check:      Does an already-installed dependency do it?
(f) Minimal Code:          Write the minimum custom code possible
(g) Test Required:         Does this change need a test in the same pass?
```

**Decision Rule**: Stop at the first step that provides a solution.

## Self-Verification Protocol

After EVERY code-writing pass, verification runs:

1. **Type checker** (if configured)
2. **Linter** (if configured)
3. **Test suite**
4. **Diff scan** for:
   - Empty catch/except blocks
   - Placeholder returns
   - Unreachable code
   - New TODO/FIXME comments

## Installation

### As a CLI tool

```bash
npm install -g wrought
```

### As an Agent Skill

#### Claude Code
```bash
mkdir -p .claude/skills/wrought
# Copy SKILL.md to .claude/skills/wrought/SKILL.md
```

#### OpenCode
```bash
mkdir -p .opencode/skills/wrought
# Copy SKILL.md to .opencode/skills/wrought/SKILL.md
```

#### Cursor
Add contents of `src/skills/SKILL.md` to `.cursorrules`

#### GitHub Copilot
Add contents of `src/skills/SKILL.md` to `.github/copilot-instructions.md`

#### Windsurf
Add contents of `src/skills/SKILL.md` to `.windsurfrules`

#### Gemini CLI
Add contents of `src/skills/SKILL.md` to `.gemini/settings.json`

## Commands

```bash
# Verify Wrought is active
wrought activate

# Run whole-repo audit
wrought audit

# Review current changes
wrought diff

# Manage debt ledger
wrought ledger add "Description" <step> <priority> <files>
wrought ledger list
wrought ledger warnings 7
```

### Command Details

| Command | Purpose |
|---------|---------|
| `wrought activate` | Verify skill is active and Node.js is available |
| `wrought audit` | Run full verification suite on current project |
| `wrought diff` | Pre-commit diff review gate |
| `wrought ledger add` | Add a debt entry with description, step, priority, and files |
| `wrought ledger list` | Show all debt entries with summary statistics |
| `wrought ledger warnings` | Show entries older than threshold (default 7 days) |

## Debt Ledger

When you MUST take a shortcut (a ladder step fails but you proceed anyway):

```bash
# Log it immediately
wrought ledger add "Skipped type checking" typecheck high src/app.ts

# Check periodically
wrought ledger warnings 7

# List all entries
wrought ledger list
```

Priorities: `low`, `medium`, `high`, `critical`

## Supported Hosts

| Host | Native Support | Fallback |
|------|----------------|----------|
| Claude Code | Yes | N/A |
| OpenCode | Yes | N/A |
| Cursor | Yes | N/A |
| GitHub Copilot | Yes | N/A |
| Windsurf | Yes | Pre-commit hook |
| Gemini CLI | Yes | CI/CD integration |

## Fallback Mode

If runtime hooks fail (Node.js missing, etc.), Wrought degrades to static rules:

1. Prints warning that dynamic verification is unavailable
2. Continues with ladder rules as text instructions
3. Manual verification required by agent

## Development

```bash
# Install dependencies
npm install

# Type-check
npm run typecheck

# Lint
npm run lint

# Run tests
npm test

# Build
npm run build

# Run CLI locally
npm run dev -- activate
```

## Architecture

```
src/
├── index.ts              # Main exports and VERSION constant
├── ladder-engine/        # YAGNI enforcement (7-step hierarchy)
├── verification/         # Post-code-pass checks (typecheck, lint, test, diff)
├── ledger/               # Debt tracking with age thresholds
├── cli/                  # Command-line interface
└── skills/               # Agent skill definitions (SKILL.md)
```

## Benchmarks

See [benchmarks/METHODOLOGY.md](benchmarks/METHODOLOGY.md) for reproducible benchmark methodology.

Known limitations:
- Language detection covers TypeScript, Python, Go, Rust
- Limited to projects under 10,000 files
- Concurrent ledger modifications use last-write-wins

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for the definition of done.

## License

MIT
