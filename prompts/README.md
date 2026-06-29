# AI Test-Generation Prompt Library

A curated set of prompts for generating **stable, maintainable** Playwright/Cypress
tests with Claude, GPT, Cursor, or Copilot. Each prompt assumes you've installed the
`CLAUDE.md` / `.cursorrules` from this kit so the assistant already knows your
conventions — these prompts then drive specific tasks.

**How to use:** copy a prompt, replace the `<…>` placeholders, paste into your assistant.
Always review and **run** the output — AI drafts fast but you own correctness.

---

## 1. Generate tests from a user story

```
Write Playwright tests for this user story, following our CLAUDE.md conventions
(POM, fixtures, web-first assertions, @smoke tagging).

User story:
"<As a [role], I want to [action] so that [outcome]>"

Acceptance criteria:
- <criterion 1>
- <criterion 2>

Cover the happy path plus the most important negative/edge case. Reuse existing page
objects in pages/ if they fit; tell me which new page-object methods you need.
```

## 2. Generate tests from a URL / live page

```
Here is the HTML/DOM of <page name> (pasted below). Generate a Playwright page object
for it using the most resilient locators available (role > label > text > test-id),
then write a @smoke test for the primary action on this page.

Do NOT use nth-child or deep CSS selectors. If the DOM lacks stable hooks, list the
data-testid attributes you'd ask the dev team to add.

<paste DOM>
```

## 3. Build a Page Object

```
Create a Playwright Page Object named <Name>Page extending BasePage, for these
interactions: <list actions, e.g. search, filter, sort>.

Requirements:
- Locators as readonly fields set in the constructor.
- Intent methods only (no raw locators exposed).
- Explicit expect* methods for assertions; keep them out of action methods.
- TypeScript strict, no any.
```

## 4. Turn a bug report into a regression test

```
We had this bug: "<bug description, repro steps, expected vs actual>".

Write a Playwright regression test that would have caught it. Name it clearly,
reference the ticket <ID> in a comment, and assert the corrected behavior. Keep it to
one behavior with a single user-observable assertion.
```

## 5. Diagnose & fix a flaky test

```
This Playwright test is flaky in CI (passes locally). Identify the ROOT CAUSE
(race condition, locator, test-data pollution, CI timing, shared state) and rewrite it
to be deterministic. Explain which cause it was and why your fix removes it. Do not mask
it with waitForTimeout or extra retries.

<paste test + the failure/trace>
```

## 6. Add API setup/teardown for deterministic data

```
This UI test depends on app state that should be set up via API, not the UI. Refactor it
to provision its required data with an API request in a fixture, run the UI assertions,
then clean up. Use our fixtures/ pattern. Show the fixture and the updated test.

<paste test>
```

## 7. Expand edge-case coverage

```
Given this page object and existing happy-path test, list the boundary values, empty
inputs, max-length, invalid-input, and permission/error scenarios we're missing. Then
write tests for the top 3 by risk (likelihood × impact). Explain the ranking briefly.

<paste page object + test>
```

## 8. Generate the GitHub Actions CI workflow

```
Write a GitHub Actions workflow that installs deps, installs the Chromium browser,
runs our Playwright suite on push and PR to main, and uploads the HTML report as an
artifact. Use npm ci, cache npm, set CI=true, cap workers at 2, and a 30-min timeout.
```

## 9. Review generated tests (use AI as a second reviewer)

```
Review this test against our CLAUDE.md rules. Flag any: hard waits, brittle selectors,
inline logins, assertions hidden in action methods, hardcoded URLs/creds, order
dependence, or multiple behaviors in one test. Output a checklist with file:line and a
suggested fix for each issue.

<paste test>
```

## 10. Convert Cypress ↔ Playwright

```
Convert this Cypress test to Playwright following our CLAUDE.md conventions (POM,
fixtures, web-first assertions). Map cy.intercept to Playwright route handling and
cy.request setup to an API fixture. Note anything that has no clean 1:1 mapping.

<paste Cypress test>
```

---

### Prompt-writing tips that keep output stable
- **Always reference your installed rules** ("following our CLAUDE.md conventions") — it
  anchors the model to your standards instead of generic output.
- **Ask for the root cause, not just a fix**, on flaky/broken tests — it prevents
  band-aid `waitForTimeout`s.
- **Demand the assistant name new page-object methods** rather than dumping selectors in
  the spec.
- **Review + run everything.** These prompts compress drafting, not your judgment.
