# Skill: troubleshoot-extension

## Metadata

-   Name: troubleshoot-extension
-   Version: 1.1.0
-   Description: Diagnoses and resolves common issues when developing or
    running AEM UI Extensions (Content Fragment Editor, Universal
    Editor, or Experience Hub) backed by Adobe App Builder.
-   Last Updated: 2026-02-13

------------------------------------------------------------------------

## Why this skill exists

UI extensions commonly fail due to configuration/registration mistakes,
missing scopes/permissions, host enablement issues, CORS/allowlist
problems, or action/runtime errors. This skill provides a fast,
structured troubleshooting workflow that reduces iteration time.

------------------------------------------------------------------------

## Agent Behavior Instructions

The agent MUST: 1. **Identify the host** (CFE vs UE vs Experience Hub) and the failing
extension point(s). 2. **Classify the failure mode** into one (or more)
categories: - Not visible in host UI - Visible but UI fails to load - UI
loads but action calls fail - Auth / permission failures -
Deployment/environment mismatches - Performance/timeouts 3. **Request or
infer minimal diagnostics** without stalling: - Relevant logs (browser
console, App Builder action logs) - Manifest/registration snippet -
app.config.yaml relevant sections - Environment variables/secrets
presence (names only, not values) 4. Provide a **ranked hypothesis
list** (most likely first). 5. Provide **step-by-step verification
actions** and what "good" looks like. 6. Provide **fix guidance** that
is: - Host-specific (CFE vs UE) - Environment-specific
(dev/stage/prod) - Safe (no secret leakage).

The agent SHOULD: - Include "known failure modes" and quick checks
(e.g., missing scope, wrong org/project, stale deployment). - Suggest a
minimal reproduction path if the issue is unclear. - Recommend
adding/confirming telemetry or correlation IDs to improve future
debugging.

The agent MUST NOT: - Ask for secret values. - Suggest bypassing
security controls to "make it work".

------------------------------------------------------------------------

## Known Failure Modes (UE + App Builder)

Use this section to fast-path diagnosis. See `docs/knowledge-base.md` for
details.

### Experience Hub

| Symptom | Likely cause | Fix |
|---------|--------------|-----|
| Extension not visible in Launchpad | Not registered in Extension Manager; wrong Extension URL | Register via Extension Manager (BYO) with correct deployed URL |
| Header button not appearing | `getButtons()` not returning array; wrong method shape | Return `[{ id, label, icon, onClick }]` from `headerMenu.getButtons()` |
| Modal fails to load | Relative URL; CORS on modal iframe | Use absolute URL from `window.location` for modal content |
| Configuration empty | Params not set in Extension Manager | Add key-value params in Extension Manager Configuration |

### UE + App Builder

| Symptom | Likely cause | Fix |
|---------|--------------|-----|
| Rail/panel not showing in UE | `register()` called on every render; relative panel URL | Wrap `register()` in `useEffect([])`; use absolute panel URL from `window.location` |
| "Unexpected end of JSON input" | `JSON.parse()` on empty or non-JSON action response | Use safe parse (try/catch with fallback) in action invocation util |
| 405 Method Not Allowed (deployed) | UI calls CDN origin `/api/...` instead of Runtime | Use build-generated `config.json` action URLs when not on localhost |
| CORS blocked (localhost → adobeioruntime.net) | Direct Runtime call from localhost | Use same-origin URL on localhost; prefer `aio app dev` for local |
| 405 on localhost | Wrong path: `default/package/action` | Local dev uses `package/action` (two segments); use `aio app dev` |

------------------------------------------------------------------------

## Required Inputs

At minimum: - Host: Content Fragment Editor, Universal Editor, or Experience Hub -
Symptoms (what the user sees) - Target environment (dev/stage/prod)

Helpful (if available): - Browser console errors - App Builder action
logs (or snippets) - Manifest/registration excerpts - app.config.yaml
excerpts - Scopes/permissions info

------------------------------------------------------------------------

## Output Contract

The output MUST include: 1. Problem summary 2. Host + extension point(s)
involved 3. Observations (from provided diagnostics) 4. Ranked
hypotheses 5. Verification checklist (step-by-step) 6. Fix plan
(specific config/code changes) 7. Prevention recommendations (tests,
logging, CI checks)

------------------------------------------------------------------------

## Example Usage Prompts

### Not visible in host UI (CFE)

""" Use troubleshoot-extension. Our Content Fragment Editor toolbar
extension is not showing up in the editor. We deployed to dev. Provide a
ranked checklist of what to verify (registration, scopes, enablement,
config). """

### Action failures (UE)

""" Use troubleshoot-extension. Our Universal Editor side panel loads,
but calling the backend action returns 401. We have browser console
errors and App Builder action logs available. Tell us what to check and
how to fix it safely. """
