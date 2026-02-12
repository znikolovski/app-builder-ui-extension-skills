# AGENTS.md

*Last updated: 2026-02-12* (includes Universal Editor extension learnings)

This document defines how AI agents should operate within a typical
**AEM UI Extensions project powered by Adobe App Builder**.

It outlines: - Project structure - Where skills are located -
Recommended workflow - Coding and security expectations -
Troubleshooting approach - Where to get help

------------------------------------------------------------------------

# 1. Project Purpose

This repository contains an **Adobe App Builder** project that
implements one or more **AEM UI Extensions**, targeting:

-   Content Fragment Editor (CFE)
-   Universal Editor (UE)

Agents operating in this repository must follow the structured skill
workflow defined in `.skills/`.

------------------------------------------------------------------------

# 2. Skills Location

All reusable agent skills are located in:

    .skills/

Each skill is defined in:

    .skills/<skill-name>/SKILL.md

Agents MUST use the skills in `.skills/` rather than redefining
workflows.

Core skills include:

-   analyze-and-plan
-   generate-sample-extension
-   scaffold-extension
-   configure-and-register
-   build-extension
-   validate-and-harden
-   distribute-extension
-   troubleshoot-extension

------------------------------------------------------------------------

# 3. Recommended Agent Workflow

Agents SHOULD follow this workflow for new UI extension features:

1.  **analyze-and-plan**
2.  **generate-sample-extension** (optional baseline validation)
3.  **scaffold-extension**
4.  **configure-and-register**
5.  **build-extension**
6.  **validate-and-harden**
7.  **distribute-extension**
8.  **troubleshoot-extension** (as needed)

Agents MUST NOT skip planning for non-trivial changes.

------------------------------------------------------------------------

# 4. Inferred Project Structure

Based on the official AEM UIX examples repository:
https://github.com/adobe/aem-uix-examples

A typical project structure should resemble:

    .
    ├── app.config.yaml
    ├── package.json
    ├── src/
    │   ├── actions/                # App Builder backend actions
    │   │   └── <action-name>/
    │   │       ├── index.js
    │   │       └── package.json
    │   │
    │   ├── web-src/                # UI extension (React-based)
    │   │   ├── components/
    │   │   ├── hooks/
    │   │   ├── utils/
    │   │   ├── index.jsx
    │   │   └── extension-registration.js
    │   │
    │   └── shared/                 # Shared utilities (optional)
    │
    ├── .skills/                    # Agent skills
    │   └── <skill-name>/
    │       └── SKILL.md
    │
    └── docs/
        └── architecture.md

Agents MUST maintain separation between:

-   UI extension code (client-side)
-   App Builder actions (server-side, privileged logic)

**Note for this repo:** Extensions are under `src/<extension-name>/` (e.g. `src/universal-editor-ui-1/`) with their own `ext.config.yaml` and `web-src/`. Root `app.config.yaml` includes them via `extensions: universal-editor/ui/1: $include: src/universal-editor-ui-1/ext.config.yaml`.

------------------------------------------------------------------------

# 5. Security Expectations

Agents MUST:

-   Never store secrets in source files
-   Use App Builder secrets / environment variables
-   Keep privileged operations inside backend actions
-   Validate IMS scopes and AEM permissions
-   Avoid exposing tokens to the client

Agents MUST NOT:

-   Suggest bypassing security controls
-   Request actual secret values in prompts

------------------------------------------------------------------------

# 6. Host Awareness

Agents MUST explicitly identify which host is targeted:

## Content Fragment Editor (CFE)

Extension points include: - Toolbar items - Context menu actions -
Custom field renderers - Modals

## Universal Editor (UE)

Extension points include: - Side panels (Properties Rail) - Header menu - Contextual UI extensions - Modal dialogs - Custom renderers

Agents must verify extension point availability using official documentation.

### Universal Editor: Properties Rail (right panel)

- Register panels with **`rightPanel.addRails()`** (not `getPanels()`). Return an array of rail objects.
- Each rail MUST include: **`id`** (unique), **`header`** (tooltip and panel title), **`url`** (panel content URL), **`icon`** (React Spectrum workflow icon name, e.g. `Pencil`, `Edit`, `TextFormat`).
- Panel **`url`** must work when loaded in an iframe by the host. For HashRouter use `/#/your-route` (e.g. `/#/hello-world-panel`).
- Main extension frame calls **`register()`**; panel content runs in a separate iframe and uses **`attach({ id: extensionId })`** to get a connection. Both connections expose `host` API (editorState, editorActions, etc.).

### Universal Editor: Editor state and selection

- **Editor state:** `guestConnection.host.editorState.get()` returns `{ selected, editables }`. `selected` is an object mapping editable id to true; find the selected editable in the `editables` array by id.
- **Text selection:** The panel runs in a different iframe from the canvas. `window.getSelection()` in the panel only sees selection inside the panel, not in the editor canvas. Do not rely on it for “selected text” in the canvas. Use the selected editable and its content from the host (e.g. from `editables` or from model when available) for display.

### Universal Editor: Updating content (editorActions.update)

- **API:** `guestConnection.host.editorActions.update({ target: { editable }, patch: JSONPatch });`
- **Limitation:** Only **one field** per call; only the **replace** operation is supported.
- **Patch path:** Use the editable’s **`prop`** field as the patch path. Normalize to a leading slash (e.g. `prop` "text" → path `"/text"`). Do not guess the path from the model.
- **Target:** Pass the editable object from editor state as `target: { editable: selectedEditable }`.
- **`host.field.getModel()`:** Often **times out** when called from the panel iframe (e.g. 10s). Prefer using the **selected editable** from `editorState.get()` for both path (via `editable.prop`) and any displayed content; avoid blocking the replace flow on `getModel()`.

### Universal Editor: UI and styling

- Use **Spectrum Provider** with `theme={defaultTheme}` and `colorScheme` from `connection.sharedContext.get("theme")` so the panel matches the host theme (light/dark).
- For alerts use **`InlineAlert`** from `@adobe/react-spectrum` (not `Alert`).

------------------------------------------------------------------------

# 7. Coding Expectations

Agents SHOULD:

-   Prefer small, modular components
-   Implement structured logging in actions
-   Include graceful loading + error states in UI
-   Optimize for cold start mitigation in actions
-   Include test and verification steps

Agents MUST NOT:

-   Mix business logic into the client if it requires privileged access
-   Assume production readiness without validate-and-harden

------------------------------------------------------------------------

# 8. Troubleshooting Principles

When issues arise:

1.  Identify host (CFE vs UE)
2.  Identify extension point
3.  Check registration
4.  Check scopes & permissions
5.  Check environment variables
6.  Review action logs
7.  Review browser console

Use the `troubleshoot-extension` skill for structured diagnostics.

**Universal Editor–specific:**

- **Panel not showing in rail:** Use `addRails()` (not `getPanels()`); include `id`, `header`, `url`, `icon`; ensure panel URL is loadable in an iframe (e.g. `/#/route` for HashRouter).
- **`host.field.getModel()` timed out:** Expected when called from the panel iframe. Use the selected editable from `editorState.get()` and the editable’s `prop` for the update path; do not block replace/update on `getModel()`.
- **Update fails or “[object Object] doesn’t exist”:** Use the editable’s **`prop`** as the patch path (with leading slash). Pass the editable from editor state as `target: { editable }`. Ensure only one replace in `patch` per call.
- **“Selected text” always empty in panel:** Panel runs in a different iframe; `window.getSelection()` does not see canvas selection. Use editor state and editable content from the host for display.

------------------------------------------------------------------------

# 9. Reference Repositories & Documentation

## UI Extensibility runtime (primary)

- **UIX SDK (GitHub):** https://github.com/adobe/uix-sdk  
  Runtime for Host/Guest communication; guest uses `register()` and `attach()`, host and guest communicate via messages (RPC). Use this as the canonical reference for extension architecture.

## Universal Editor API (Adobe Developer)

- **Common concepts & extension points:** https://developer.adobe.com/uix/docs/services/aem-universal-editor/api/commons
- **Actions (navigateTo, refreshPage, selectEditables, update, reloadExtension):** https://developer.adobe.com/uix/docs/services/aem-universal-editor/api/actions  
  **Update action:** https://developer.adobe.com/uix/docs/services/aem-universal-editor/api/actions/#update — single argument `{ target: { editable }, patch: [{ op: "replace", path, value }] }`; one field per call.
- **Data (editor state, shared context):** https://developer.adobe.com/uix/docs/services/aem-universal-editor/api/data
- **Properties Rail (custom panels):** https://developer.adobe.com/uix/docs/services/aem-universal-editor/api/properties-rails — `addRails()`, rail shape: `id`, `header`, `url`, `icon` (required).
- **Events:** https://developer.adobe.com/uix/docs/services/aem-universal-editor/api/events

## React Spectrum (UI and icons)

- **Workflow icons (for rail `icon`):** https://react-spectrum.adobe.com/react-spectrum/workflow-icons.html  
  Use exact icon names (e.g. `Pencil`, `Edit`, `TextFormat`). Use **`InlineAlert`** for alerts, not `Alert`.

## Example Implementations

- AEM UIX Examples: https://github.com/adobe/aem-uix-examples

## App Builder & AEM

- App Builder Guides: https://developer.adobe.com/app-builder/docs/guides/app_builder_guides/
- AEM UI Extensibility: https://experienceleague.adobe.com/en/docs/experience-manager-learn/cloud-service/developing/extensibility/ui/overview?lang=en
- Universal Editor Extensibility: https://experienceleague.adobe.com/en/docs/experience-manager-cloud-service/content/implementing/developing/universal-editor/extending#extending-ui

------------------------------------------------------------------------

# 10. Agent Operating Principles

Agents operating in this repository:

-   Must follow structured skills
-   Must produce structured outputs when requested
-   Must keep host-specific logic explicit
-   Must design for dev → stage → prod promotion
-   Must include verification steps in all implementation outputs

Agents should optimize for:

-   Developer velocity
-   Security
-   Production reliability
-   Clear separation of concerns
