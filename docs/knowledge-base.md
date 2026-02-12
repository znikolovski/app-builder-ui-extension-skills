# Knowledge base: AEM UI Extensions (Universal Editor + Content Fragment Editor)

Last updated: 2026-02-12

This skills library is intended to support building **AEM UI
Extensions** using **Adobe App Builder**, including:

-   **Content Fragment Editor** extensions (toolbar, context menu,
    custom fields, modals, etc.)
-   **Universal Editor** UI extensions (extending the editor UI via
    defined extension points)

## Official references (start here)

### UI Extensibility overview (AEM + App Builder)

-   AEM UI extensibility overview:
    https://experienceleague.adobe.com/en/docs/experience-manager-learn/cloud-service/developing/extensibility/ui/overview

### Content Fragment Editor extensibility

-   Overview:
    https://experienceleague.adobe.com/en/docs/experience-manager-learn/cloud-service/developing/extensibility/ui/content-fragments/overview
-   Example: add a custom field:
    https://experienceleague.adobe.com/en/docs/experience-manager-learn/cloud-service/developing/extensibility/ui/content-fragments/examples/editor-custom-field
-   Product docs: customizing CF console/editor:
    https://experienceleague.adobe.com/en/docs/experience-manager-cloud-service/content/implementing/configuring-and-extending/content-fragments-console-and-editor

### Universal Editor extensibility

-   Extending Universal Editor (product docs):
    https://experienceleague.adobe.com/en/docs/experience-manager-cloud-service/content/implementing/developing/universal-editor/extending#extending-ui
-   Universal Editor extensibility docs (UIX developer docs):
    https://developer.adobe.com/uix/docs/services/aem-universal-editor/
-   Universal Editor extension points API docs:
    https://developer.adobe.com/uix/docs/services/aem-universal-editor/api/

## What the agent should capture during planning

When planning any UI extension, the agent should explicitly identify:

1.  **Extension target**
    -   Content Fragment Editor **or** Universal Editor (or both)
2.  **Extension point(s)**
    -   The specific extension point(s) being used (toolbar item, custom
        field renderer, etc.)
3.  **Data and context**
    -   What context the host UI provides (selection, entity
        identifiers, authoring context)
4.  **Backend needs**
    -   Which actions are required, what they call (AEM APIs, external
        APIs), and what must be kept server-side
5.  **Security + permissions**
    -   IMS scopes, AEM permissions, and how tokens/credentials are
        handled
6.  **Authoring experience**
    -   UX expectations: latency, progressive loading, failure states,
        accessibility
7.  **Environments and rollout**
    -   Dev/stage/prod separation and promotion steps

## Known-good sources of examples

-   GitHub examples repo: https://github.com/adobe/aem-uix-examples
