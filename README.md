# Adobe App Builder -- AEM UI Extension Skills Library

This repository contains structured agent skills for designing,
building, and distributing **Adobe App Builder** applications that power
**AEM UI Extensions**.

It supports: - **Content Fragment Editor** extensions - **Universal
Editor** UI extensions

## Recommended workflow

1.  **analyze-and-plan** --- confirm host (CFE/UE), extension points,
    architecture, security, environments
2.  **generate-sample-extension** *(optional)* --- generate a known-good
    reference sample for the chosen host/extension point
3.  **scaffold-extension** *(optional but recommended)* --- generate
    minimal boilerplate for the chosen host/extension points
4.  **configure-and-register** --- apply/verify manifests, environment
    variables, scopes, host enablement
5.  **build-extension** --- implement UI + actions and validate in dev
6.  **validate-and-harden** *(recommended before prod)* ---
    security/performance/observability/resilience review
7.  **distribute-extension** --- package, deploy/promote, release
    notes + rollback
8.  **troubleshoot-extension** --- use anytime issues arise (visibility,
    auth, action failures, perf)

## Skills

-   analyze-and-plan
-   generate-sample-extension
-   scaffold-extension
-   configure-and-register
-   build-extension
-   validate-and-harden
-   distribute-extension
-   troubleshoot-extension

## Knowledge base

See: `docs/knowledge-base.md` for curated official references and
planning checkpoints.

## Repository structure

    skills/
      analyze-and-plan/
        skill.md
      generate-sample-extension/
        skill.md
      scaffold-extension/
        skill.md
      configure-and-register/
        skill.md
      build-extension/
        skill.md
      validate-and-harden/
        skill.md
      distribute-extension/
        skill.md
      troubleshoot-extension/
        skill.md
    docs/
      knowledge-base.md

Generated on 2026-02-12
