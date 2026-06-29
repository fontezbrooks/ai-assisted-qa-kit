# AI-Assisted QA Kit

**Make your AI coding assistant write tests like a senior SDET.**

Cursor, Claude Code, and Copilot are great at *most* code — and terrible at tests by
default. They reach for `waitForTimeout`, brittle `nth-child` selectors, and copy-paste
logins, producing suites that pass today and flake next week. This kit installs the
standards that fix that, so AI generates **stable, maintainable, CI-ready** Playwright
and Cypress tests from the first try.

Built by a working SDET. Drop-in. ~10 minutes to install.

---

## Why this exists

> 72.8% of testers rank AI test-generation their #1 priority for 2026 — but only ~16%
> have adopted it. The gap isn't the AI; it's that the AI doesn't know *your* testing
> standards. This kit encodes them.

The AI-config wave (CLAUDE.md, `.cursorrules`, MCP) is exploding for app development —
but almost nothing targets **test automation specifically**. This kit fills that gap for
Playwright and Cypress.

See [`examples/`](examples/README.md) for a same-prompt before/after that shows exactly
what changes.

---

## What's inside

```
ai-assisted-qa-kit/
├── claude/
│   ├── playwright-CLAUDE.md     # QA-tuned rules for Claude Code (Playwright)
│   └── cypress-CLAUDE.md        # ... and Cypress
├── cursor/
│   ├── .cursorrules             # legacy single-file Cursor rules
│   └── rules/
│       ├── playwright.mdc       # modern Cursor project rules (auto-scoped by globs)
│       └── cypress.mdc
├── prompts/
│   └── README.md                # 10-prompt test-generation library
├── mcp/
│   ├── README.md                # why + how to wire MCP for QA
│   └── mcp-config.example.json  # browser, Postgres test-data, GitHub, filesystem
├── examples/
│   └── README.md                # before/after proof
└── free-starter/
    └── README.md                # trimmed teaser to publish as a funnel
```

---

## Install (pick your assistant)

**Claude Code**
1. Copy `claude/playwright-CLAUDE.md` (and/or `cypress-CLAUDE.md`) to your project root as
   `CLAUDE.md`, or into `.claude/CLAUDE.md`.
2. Edit the **Project specifics** block (app name, base URL, directories).
3. Start Claude Code — it now follows the rules automatically.

**Cursor**
- *Modern:* copy `cursor/rules/*.mdc` into your project's `.cursor/rules/`. They auto-apply
  to matching files via their `globs`.
- *Legacy:* copy `cursor/.cursorrules` to your project root.

**Any assistant**
- Use the [prompt library](prompts/README.md) — each prompt references "our CLAUDE.md
  conventions" so output stays anchored to these standards.

**Optional but powerful:** wire up [MCP](mcp/README.md) so the assistant inspects your
live DOM and test DB instead of guessing locators.

---

## The standards this enforces

- One behavior + one user-observable assertion per test.
- Page Object Model — selectors out of specs, intent methods only.
- Fixtures/custom commands for setup; one-line `loggedIn` state, no inline logins.
- **No hard waits** — web-first auto-retrying assertions and network interception.
- Resilient locators: role → label → text → test-id. Never nth-child/deep CSS.
- Deterministic, self-provisioned data; no order-dependent tests.
- Env-driven config; no hardcoded URLs or secrets.
- `@smoke` tagging for critical paths; CI-aware retries and artifacts.

---

## Works great with

The [Playwright E2E Starter Kit](../playwright-e2e-starter-kit) — a runnable repo whose
structure these rules are tuned for. Use them together: scaffold with the starter kit,
generate new tests with this kit.

---

## A note on using AI honestly

AI compresses the *drafting* of tests, not your judgment. Always **review and run** what
it generates — these rules dramatically raise the floor of AI output, but you own
correctness. (Rigorous 2025 research found devs often *feel* faster with AI than they
are; treat generated tests as a first draft to verify, not a finished product.)

---

## License

See [LICENSE](LICENSE). Personal + commercial use in your own projects permitted;
redistribution/resale of the kit itself is not.

---

*Want this set up against your actual test suite, or a flaky CI suite stabilized?
That's my day job — reach out.*
