# Skill: build-extension

## Metadata

-   Name: build-extension
-   Version: 1.4.0
-   Description: Implements and validates an AEM UI Extension (Content
    Fragment Editor or Universal Editor) using Adobe App Builder based
    on an approved plan.
-   Last Updated: 2026-02-13

------------------------------------------------------------------------

## When to use

Use this skill after `analyze-and-plan` and (optionally)
`scaffold-extension` to: - Implement UI + actions - Wire host context to
actions - Validate locally and in a dev host environment.

------------------------------------------------------------------------

## Agent Behavior Instructions

When executing this skill, the agent MUST:

1.  **Follow the approved plan** (no scope creep).
2.  **Keep UI and backend concerns separate**:
    -   UI extension: React/SPA, host context handling, UX,
        accessibility.
    -   App Builder actions: secure calls to AEM/external APIs, secrets,
        validation.
3.  **Secure by default**:
    -   Never hardcode secrets; use App Builder secrets / environment
        variables.
    -   Never place privileged operations in the client.
4.  **Add structured logging + basic telemetry hooks** to actions and
    key UI flows.
5.  **Host-specific validation**:
    -   Confirm the extension behaves correctly in the intended host
        (CFE, UE, or Experience Hub).
6.  **Provide complete testing instructions**:
    -   Local tests (use `aio app dev` for local action invocation),
        action invocation tests, host integration steps.

**UI components:** Prefer React Spectrum (`@adobe/react-spectrum`) over
custom DOM (raw tables, styled divs). Use `TableView`, `Button`, `Link`,
`Flex`, `InlineAlert`, etc. for consistency with host UI and accessibility.

The agent SHOULD: - Prefer small, composable modules. - Include robust
error handling and user-friendly failure states. - Call out dependencies
on AEM APIs, CORS, allowlists, or permissions.

The agent MUST NOT: - Introduce new extension points beyond the plan. -
Store sensitive data in client-side state. - Assume production readiness
(that is handled by `validate-and-harden` and `distribute-extension`).

------------------------------------------------------------------------

## Experience Hub: Required Patterns

When building Experience Hub extensions (aem/launchpad/1), the agent MUST apply:

-   **headerMenu.getButtons:** Return array of `{ id, label, icon, onClick }`. Wrap in `useEffect` with empty deps if using React.
-   **Modal URL:** Build absolute URL for modal content, e.g. `${window.location.origin}${window.location.pathname || '/'}#/modal-route`.
-   **attach in modal:** Modal content runs in iframe; use `attach({ id: extensionId })` to get connection and access `host.modal.close()`, `sharedContext`, `configuration`.
-   **Configuration:** Key-value params from Extension Manager; read via `guestConnection.configuration`.

## Universal Editor: Required Patterns

When building UE extensions, the agent MUST apply these patterns to avoid
common failures:

### Properties Rail registration

-   Wrap `register()` in `useEffect` with empty deps so it runs once.
    Calling `register()` on every render causes duplicate registration
    and unstable rails.
-   Use `addRails()` (not `getPanels()`). Each rail MUST have: `id`,
    `header`, `url`, `icon`.
-   Panel `url` must be absolute and iframe-loadable. Build it from
    `window.location.origin` + `pathname` + `#/route` for HashRouter.
    Example: `` `${window.location.origin}${window.location.pathname || '/'}#/my-panel` ``

### Action invocation from UI

-   **Local dev (localhost):** Use same-origin URL so the dev server
    proxies to Runtime and avoids CORS:
    `` `${origin}/api/v1/web/${package}/${action}` ``
    Path format is **two segments**: package/action, NOT default/package/action.
-   **Deployed:** Use build-generated `config.json` (action URLs point
    to Runtime). The build writes correct URLs; do not construct URLs
    pointing to the CDN origin (causes 405).
-   **Prefer `aio app dev`** for local development: actions run locally,
    no CORS, URL format is package/action. With `aio app run`, UI is
    local but actions are on Runtime, so direct Runtime calls from
    localhost hit CORS.

### Action response handling

-   Use a safe JSON parse: action responses can be empty, HTML error
    pages, or non-JSON. Wrapping `JSON.parse` in try/catch with a
    fallback prevents "Unexpected end of JSON input" crashes.

------------------------------------------------------------------------

## Required Inputs

-   Approved technical plan from analyze-and-plan
-   App Builder project configuration (provided by the user)
-   Target AEM environment details (URLs, org/program identifiers as
    applicable)
-   API credentials/scopes available to the project

------------------------------------------------------------------------

## Output Contract

The output MUST include:

1.  **Folder/File Structure** (what to create/modify)
2.  **Backend Action Implementations** (or clear patch-style guidance if
    code is too large)
3.  **UI Component Implementation**
    -   Host SDK integration points, context usage, loading/error states
4.  **Configuration Updates**
    -   app.config.yaml snippets
    -   extension manifest/registration snippets (host-specific)
5.  **Local Testing Instructions**
6.  **Integration Testing Steps**
    -   Host-specific checks for CFE vs UE vs Experience Hub
7.  **Known Issues / Follow-ups**

------------------------------------------------------------------------

## Example Usage Prompts

### Content Fragment Editor (CFE)

""" Use build-extension to implement the planned Content Fragment Editor
toolbar + modal extension. The modal should show the current field
value, call the backend action for rewrite suggestions, and allow
writing the selected suggestion back to the fragment. """

### Universal Editor (UE)

""" Use build-extension to implement the planned Universal Editor panel
extension. It should read the current authoring context from the host,
fetch validation results from a backend action, and render a checklist
with fix actions. """

### Experience Hub

""" Use build-extension to implement the planned Experience Hub
extension with header menu button and modal. The modal should display
shared context (aemHost, auth) and extension configuration. """
