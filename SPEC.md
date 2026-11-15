# Web Search Override Boundary

## Problem Statement

Pi users can invoke the `web_search` tool without specifying a search model or provider. However, the Pi-facing tool contract currently exposes optional `model` and `provider` fields. In observed calls, models have supplied those optional fields as empty strings even when the prompt guidance says to omit them. An empty `model` value reaches search-model validation and causes the request to fail with `model must be a non-empty model ID`.

This failure is especially confusing because the user did not request a model or provider override, and the ordinary search operation does not need either field. Stronger prompt wording and parameter descriptions were tested in a rebuilt bundle and a fresh session, but malformed empty optional fields were still emitted. The tool contract therefore permits a failure mode that prose guidance does not reliably prevent.

The change must also preserve the distinction between the ordinary Pi model-facing tool boundary and other callers. Runtime support for explicit model and provider overrides is used by programmatic callers and MCP-related paths and must not be removed without a separate compatibility decision.

## Solution

Make the ordinary Pi-facing `web_search` contract omit `model` and `provider` from its exposed input schema. The default Pi search flow will therefore not invite models to populate either override.

Add a defensive boundary at argument preparation and execution so that stale schemas, adapters, or direct callers that still provide `model` or `provider` cannot pass those fields into the ordinary Pi search execution path. Existing query-array normalization remains unchanged.

Keep the underlying search runtime and its explicit override validation available to programmatic and MCP callers. The fix changes what the ordinary Pi tool accepts and forwards, not the general search capability.

## User Stories

1. As a Pi user, I want an ordinary web search to work without choosing a search model, so that I can search without understanding provider-specific configuration.
2. As a Pi user, I want an ordinary web search to work without choosing a search provider, so that the configured default routing remains transparent.
3. As a Pi user, I want the web search tool not to fail because an unused optional field was sent as an empty string, so that irrelevant model output does not interrupt my research.
4. As a Pi user, I want the tool schema to expose only the arguments needed for ordinary search, so that the model receives a smaller and less ambiguous contract.
5. As a Pi user, I want omitted search overrides to remain omitted, so that default routing is not accidentally replaced by an empty or invalid override.
6. As a Pi user, I want stringified query arrays to continue being normalized, so that existing model argument formats remain usable.
7. As a Pi user, I want ordinary searches to continue supporting queries, result limits, content inclusion, recency filters, domain filters, categories, workflows, and proxies, so that removing unrelated overrides does not reduce normal search functionality.
8. As a Pi user, I want malformed override fields from an older cached tool definition to be ignored at the tool boundary, so that a session does not fail solely because it retained an earlier schema.
9. As a Pi user, I want malformed override fields supplied through a direct execution path to be ignored, so that all ordinary Pi execution paths have the same safe behavior.
10. As a Pi user, I want the configured default provider selection to remain unchanged, so that this fix does not silently change search routing.
11. As a Pi user, I want provider allowlists to continue governing configured search routing, so that the schema boundary does not weaken provider policy.
12. As a developer integrating the search runtime, I want explicit model overrides to remain available to supported programmatic callers, so that existing integrations do not lose deliberate model selection.
13. As an MCP caller, I want the existing MCP search behavior to remain available, so that this Pi-specific compatibility fix does not break MCP clients.
14. As a developer, I want runtime validation for explicit model overrides to remain intact outside the ordinary Pi boundary, so that invalid model identifiers continue to produce useful validation errors where overrides are supported.
15. As a developer, I want provider selection for supported non-Pi callers to remain intact, so that explicit provider routing remains a deliberate integration feature.
16. As a developer, I want the ordinary Pi tool to reject accidental override propagation by omission from its schema and by defensive stripping, so that one protection does not depend on another.
17. As a developer, I want the tool definition to remain stable across environments except for intentional configuration-dependent content, so that tool caching and definition comparisons remain predictable.
18. As a maintainer, I want the change to be narrow and localized to the Pi web-search registration boundary, so that it can be reviewed and reverted without a broader policy redesign.
19. As a maintainer, I want tests to verify externally observable tool behavior rather than implementation details, so that the regression suite protects the user-facing failure mode.
20. As a maintainer, I want a regression test to prove that the exposed Pi schema has no `model` property, so that the original failure path cannot quietly return.
21. As a maintainer, I want a regression test to prove that the exposed Pi schema has no `provider` property, so that the ordinary Pi contract does not invite unnecessary routing overrides.
22. As a maintainer, I want a regression test to prove that stale `model` and `provider` arguments are removed before execution, so that compatibility behavior is explicit.
23. As a maintainer, I want a regression test to prove that a search with empty override fields completes successfully, so that the original validation error is covered at the highest practical seam.
24. As a maintainer, I want existing search-model override tests for supported lower-level callers to remain meaningful, so that the Pi schema change is not mistaken for removal of runtime override support.
25. As a user running a new Pi session, I want the newly registered tool definition to reflect the safe contract, so that the fix takes effect when the extension is loaded.
26. As a user with an existing Pi session, I want stale arguments to be handled defensively, so that the session fails safely even before its tool definition is refreshed.
27. As a developer, I want the ordinary Pi search path to pass only normalized search parameters to execution, so that provider and model selection cannot be triggered accidentally by leftover fields.
28. As a maintainer, I want the patch to avoid adding a new configuration option or normalization policy system, so that the fix addresses this concrete compatibility issue without increasing project complexity.

## Implementation Decisions

- Modify the Pi-facing web-search tool registration rather than changing the lower-level search engine. The registration boundary is the highest existing seam that directly controls the schema presented to Pi models.
- Remove the dynamically generated `model` property from the ordinary Pi web-search input schema.
- Remove the `provider` property from the ordinary Pi web-search input schema. Provider selection remains available through the configured default and through supported non-Pi interfaces.
- Remove the now-unused type imports and model-provider discovery used only to construct the removed Pi schema property.
- Retain the shared provider-description logic where it is still needed by other tool definitions, including source checking.
- Preserve all existing ordinary search parameters unrelated to model and provider overrides.
- Extend argument preparation so it first performs the existing stringified-array normalization, then removes `model` and `provider` from object arguments before returning them to the tool system.
- Preserve the existing behavior for non-object or array-shaped prepared arguments rather than introducing a new general argument-normalization policy.
- Add a second defensive removal at web-search execution. This protects direct execution paths and callers that bypass argument preparation.
- Treat the stripped values as ignored input, not as a request to select a fallback model or provider. No silent substitution, current-agent-model copying, or provider inference is introduced.
- Leave lower-level model and provider validation unchanged. Explicit override support remains a runtime capability for callers whose contracts still expose it.
- Leave MCP definitions and MCP execution behavior unchanged unless an existing test demonstrates that the shared implementation makes that impossible. The intended boundary is Pi registration, not MCP compatibility.
- Do not rely on prompt wording or parameter descriptions as enforcement. Descriptions may guide a model, but schema omission and runtime stripping provide the actual protection.
- Do not add configuration, feature flags, a new policy layer, or broad argument normalization for this fix.
- Build the installed extension bundle from the modified source and deploy only the generated Pi extension bundle permitted by the surrounding environment. Source-package changes and unrelated generated artifacts are outside this implementation unless the repository workflow requires them.

## Testing Decisions

- Test the highest practical seam: initialize the extension with a minimal Pi API, inspect the registered `web_search` tool, prepare arguments, and execute a mocked search. This single registration seam covers the exposed schema, preparation behavior, and ordinary execution behavior.
- Test external behavior, not local implementation names or destructuring details. A test should assert what properties the tool exposes, what arguments reach the search operation, and whether the operation succeeds.
- Assert that the ordinary Pi web-search schema does not contain `model`.
- Assert that the ordinary Pi web-search schema does not contain `provider`.
- Assert that valid ordinary search properties remain present, including query input and query arrays.
- Assert that stringified query arrays continue to become arrays.
- Assert that input containing `model: ""` and `provider: ""` is prepared without either field.
- Assert that direct execution with those stale fields also succeeds and does not produce the non-empty-model validation error.
- Mock the external search response so the regression test isolates argument handling from network availability and provider credentials.
- Keep or adapt existing model-override tests for lower-level and programmatic callers. Those tests should continue to demonstrate that explicit overrides work where their interface still exposes them.
- Keep or adapt existing tool-registration tests to reflect the intended ordinary Pi schema. The registration test is the prior art for asserting tool definitions and configuration-dependent registration behavior.
- Keep or adapt existing tool-argument tests to cover preparation and normalization behavior. The new regression should extend that seam rather than create a parallel argument-processing harness.
- Run the repository's type-checking command using the command supported by the current package scripts, verify that it performs actual checking, and do not treat compiler help output as a successful type check.
- Run the repository test suite with test paths passed using the runner's supported syntax. Avoid passing multiple paths as one invalid argument.
- Build the extension bundle after tests pass.
- Run whitespace and patch-integrity checks, including a diff check, before deployment.
- Verify the installed bundle exposes the changed schema and executes the malformed-input smoke test successfully.
- Verify that only the permitted generated bundle was changed in the installed package.

## Out of Scope

- Removing model or provider override support from the lower-level search runtime.
- Removing model or provider support from programmatic callers or MCP clients.
- Replacing explicit provider allowlists or routing configuration with a new policy system.
- Automatically selecting a model based on the active Pi agent model.
- Normalizing empty strings into `undefined` across every web-access tool.
- Changing the semantics of an explicitly supported model or provider override for callers that still expose those fields.
- Changing search-provider availability detection or provider credentials.
- Changing search result ranking, query rewriting, content extraction, summarization, curator behavior, proxy behavior, or fetch behavior.
- Adding a new user-facing configuration option.
- Relying on stronger prompt prose as the primary fix.
- Changing unrelated generated bundles or source modules.
- Creating a new Pi conversation solely for validation. If one is created for testing, its session artifact must be removed afterward.
- Publishing an issue, applying tracker labels, or performing triage.

## Further Notes

The observed failure pattern was an object containing empty optional fields such as `model: ""`, `provider: ""`, and `proxy: ""`. The relevant failure was the empty model value reaching model-override validation. A rebuilt bundle with stronger prompt and schema descriptions was tested in a fresh session and still produced empty override fields, which is the evidence for treating schema omission as the primary fix.

The intended ordinary Pi input shape is the smallest useful search request, for example:

```json
{
  "queries": ["example query"],
  "numResults": 8
}
```

The defensive boundary should make an input such as this safe for ordinary Pi execution:

```json
{
  "queries": ["example query"],
  "model": "",
  "provider": ""
}
```

After preparation, the override fields should be absent. The expected result is a normal search attempt, not a model-validation error.

This specification is written into the previously empty project directory as a standalone implementation spec. No issue tracker or triage workflow is required for this document.
