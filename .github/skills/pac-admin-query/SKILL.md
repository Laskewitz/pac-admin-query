---
name: pac-admin-query
description: Build and run Power Platform inventory queries with pac admin query. Use when a user wants to list, filter, count, aggregate, export, or inspect tenant-wide Power Apps, Power Automate flows, Copilot Studio agents, environments, environment groups, connectors, or other Power Platform inventory resources.
---

# PAC Admin Query

Use `pac admin query` to query the tenant-wide Power Platform inventory API.

Documentation:

- [Power Platform inventory API](https://learn.microsoft.com/power-platform/admin/inventory-api)
- [Power Platform inventory schema](https://learn.microsoft.com/power-platform/admin/inventory-schema)

## Prerequisites

- Use a PAC CLI build that includes `pac admin query`.
- An active PAC authentication profile must provide tenant context and permission to call the Power Platform inventory API.
- Use `pac auth list` to inspect profiles and `pac auth select` when the correct profile isn't active.

## Command

Prefer a query file over inline JSON because it avoids shell-escaping errors.

```powershell
pac admin query --query-file .\query.json
```

Arguments:

| Argument | Alias | Purpose |
| --- | --- | --- |
| `--query-file` | `-qf` | Read the request body from a `.json` file. |
| `--query` | `-q` | Supply the request body inline. Mutually exclusive with `--query-file`. |
| `--output-type` | `-ot` | `Grid` (default), `List`, or `Json`. |
| `--output-file` | `-of` | Write output to a file. `Grid` writes CSV; `Json` writes the API response JSON. |

Examples:

```powershell
pac admin query -qf .\query.json -ot Grid
pac admin query -qf .\query.json -ot Grid -of .\inventory.csv
pac admin query -qf .\query.json -ot Json -of .\inventory.json
```

## Workflow

1. Determine the requested resource types, fields, filters, grouping, sort order, result limit, and output format.
2. Start with the closest bundled example and copy it to a working `.json` file. Don't modify the bundled reference unless the user asks to update the example.
3. Adapt only the required clauses and fields. Consult the inventory schema before using a property that isn't in an example.
4. Validate that the root is a JSON object with a nonempty `TableName` and a `Clauses` array.
5. Run the query with `--query-file`. Use `Grid` for concise tabular results and `Json` when metadata, nested values, or pagination tokens matter.
6. If the response contains a nonempty `skipToken`, put it in `Options.SkipToken` and rerun to retrieve the next page. Keep the other query clauses unchanged.

## Request Shape

```json
{
  "TableName": "PowerPlatformResources",
  "Clauses": [],
  "Options": {
    "Top": 100,
    "Skip": 0,
    "SkipToken": ""
  }
}
```

Clause order matters because clauses are translated into a KQL pipeline.

Common clause types:

- `where`: filter records.
- `project`: select or rename columns.
- `extend`: add a computed column.
- `orderby`: sort results.
- `take`: cap results in the query pipeline.
- `distinct`: return unique combinations.
- `count`: count matching records.
- `summarize`: aggregate records.
- `join`: enrich records with another inventory query.

The documented clause and `summarize` operator surface is limited. The [inventory API](https://learn.microsoft.com/power-platform/admin/inventory-api) lists these clause types plus `where`, `project`, `extend`, `orderby`, `take`, and documents only `count` and `argmax` as `summarize` operators. The advanced connector example additionally uses `mvexpand` and the `make_set` summarize operator, which are **not documented** but work empirically against the live API. Treat them as experimental.

Values are KQL expressions serialized as JSON strings. Preserve required KQL quoting, for example:

```json
{
  "$type": "where",
  "FieldName": "type",
  "Operator": "==",
  "Values": ["'microsoft.powerapps/canvasapps'"]
}
```

## Bundled Query Examples

- [canvas-apps.json](./canvas-apps.json): Simple canvas app list with selected fields.
- [environments.json](./environments.json): Environment name, region, type, managed status, and group.
- [resource-count-by-type-and-location.json](./resource-count-by-type-and-location.json): Aggregate inventory counts.
- [resources-by-environment-and-date.json](./resources-by-environment-and-date.json): Template with environment and creation-date placeholders.
- [recent-resources-summary.json](./recent-resources-summary.json): Compact ten-row cross-resource inventory query based on the supplied `q1.json`.
- [resources-with-environments-and-connectors.json](./resources-with-environments-and-connectors.json): Advanced admin-center-style query based on the supplied `q.json`; joins environment and environment-group details and aggregates connector IDs.

The advanced connector example uses `mvexpand` and `make_set`. Reuse that known query shape for connector aggregation rather than inventing new unsupported clause forms.

The `subType` filter in the recent-resources and connector examples lists `appBuilderApp`, `copilotCanvasApp`, `byocApp`, `vibeApp`, `powerAppsApp`, and `''` (empty, for resource types without a subtype). Per the [inventory schema](https://learn.microsoft.com/power-platform/admin/inventory-schema), only `appBuilderApp`, `byocApp`, and `vibeApp` are documented; `copilotCanvasApp` and `powerAppsApp` are empirical. Adjust the list to the subtypes you actually want, and don't drop `vibeApp` if you need code apps.

## Resource Types

Use exact inventory type values:

| Resource | Type |
| --- | --- |
| Canvas apps | `microsoft.powerapps/canvasapps` |
| Model-driven apps | `microsoft.powerapps/modeldrivenapps` |
| Code apps | `microsoft.powerapps/codeapps` |
| App Builder apps | `microsoft.powerapps/apps` |
| Cloud flows | `microsoft.powerautomate/cloudflows` |
| Agent flows | `microsoft.powerautomate/agentflows` |
| Workflow agent flows | `microsoft.powerautomate/m365agentflows` |
| Copilot Studio agents | `microsoft.copilotstudio/agents` |
| Environments | `microsoft.powerplatform/environments` |
| Environment groups | `microsoft.powerplatform/environmentgroups` |

## Output Guidance

- Project scalar fields before using `Grid`; nested objects and arrays are difficult to read in a table.
- Use column aliases in `project`, such as `"Name = properties.displayName"`.
- Use `Json` for nested `properties`, connectors, API metadata, and pagination.
- Use `Grid` with `--output-file` for CSV exports.
- Keep `Options.Top` conservative during query development, then increase it for the final export.
- Never claim the query covers the entire tenant when `resultTruncated` is set or a `skipToken` remains.

## Troubleshooting

- **Tenant context is required**: select or create an authentication profile with tenant context.
- **Invalid JSON**: validate the file and prefer `--query-file` over `--query`.
- **No results**: verify the exact resource type, field path, KQL literal quoting, environment ID, and date range.
- **Grid columns are unhelpful**: add a `project` clause with scalar fields or switch to `Json`.
- **Missing records**: inspect `resultTruncated` and `skipToken`, then continue paging.
