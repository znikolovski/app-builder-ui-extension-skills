# Skill: scaffold-extension

## Metadata

-   Name: scaffold-extension
-   Version: 1.1.0
-   Description: Generates a minimal, host-specific scaffold for an AEM
    UI Extension (Content Fragment Editor or Universal Editor) inside an
    existing App Builder project.
-   Last Updated: 2026-02-13

------------------------------------------------------------------------

## Why this skill exists

Teams often waste time re-creating the same boilerplate. This skill
creates a consistent baseline structure so `build-extension` can focus
on business logic.

------------------------------------------------------------------------

## Agent Behavior Instructions

The agent MUST: 1. Confirm the target host: **CFE**, **UE**, or **Experience Hub**. 2.
Scaffold only what is required for the selected extension point(s). 3.
Keep the scaffold minimal and easy to extend: - Separate UI extension
code, shared utils, and backend actions. - Use React Spectrum components
rather than custom DOM. 4. Include placeholders for: -
Host context extraction - Action invocation wrapper (with `getActionUrl`
that switches local vs deployed; safe parse for responses) - Error
boundary + loading states 5. Produce a patch-style output (what
folders/files to add) rather than a large monolithic paste when
possible.

The agent SHOULD: - Provide a "first-run" checklist to verify the
scaffold loads in the host. - Reference `docs/knowledge-base.md` for
host setup expectations.

The agent MUST NOT: - Implement full business logic (that's
`build-extension`). - Add extra extension points beyond the requested
ones.

------------------------------------------------------------------------

## Experience Hub + App Builder: Patterns to scaffold

When scaffolding an Experience Hub extension (aem/launchpad/1):

-   **Registration:** `register({ id, metadata, methods: { headerMenu: { getButtons() { return [{ id, label, icon, onClick }] } } } })`
-   **Modal:** `guestConnection.host.modal.showUrl({ title, url })` and `guestConnection.host.modal.close()`
-   **Configuration:** Extension Manager key-value params via `guestConnection.configuration`
-   **Shared context:** `aemHost`, `auth`, `theme`, `locale` from `sharedContext.get()`
-   Panel content runs in iframe; use `attach({ id })` like UE. Reference `docs/knowledge-base.md` for Experience Hub patterns.

## UE + App Builder: Utils to scaffold

When scaffolding a UE extension that invokes actions, include:

-   **`getActionUrl(actionName)`** – Returns action URL. On localhost:
    same-origin `/api/v1/web/${package}/${action}`. Otherwise: from
    `config.json` (build-generated) with https upgrade if needed.
-   **`actionWebInvoke`** – POST/GET to action URL with safe JSON parse
    (try/catch, fallback for empty/invalid responses).

------------------------------------------------------------------------

## Required Inputs

-   Target host: Content Fragment Editor, Universal Editor, or Experience Hub
-   Chosen extension point(s)
-   Existing App Builder project layout (or allow the agent to propose a
    standard layout)

------------------------------------------------------------------------

## Output Contract

The output MUST include: 1. Proposed folder structure 2. Files to add
with short purpose statements 3. Minimal manifest/registration stubs
needed to load the UI 4. "First-run" verification steps in the host
editor.

------------------------------------------------------------------------

## Example Usage Prompts

### CFE

""" Use scaffold-extension to create the minimal scaffold for a Content
Fragment Editor toolbar button + modal extension. """

### UE

""" Use scaffold-extension to create the minimal scaffold for a
Universal Editor side panel extension. """

### Experience Hub

""" Use scaffold-extension to create the minimal scaffold for an
Experience Hub extension with a header menu button and modal. """
