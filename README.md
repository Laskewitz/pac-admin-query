# 🔍 Query your Power Platform Inventory by using `pac admin query`

> [!NOTE]
> The bundled `pac-admin-query` agent skill was written by **[Andrew Petrochuk](https://github.com/petrochuk)**. Thanks, Andrew!

Version `2.9.3` of the Power Platform CLI added a new command, `pac admin query`, for querying your tenant-wide Power Platform inventory. This repo gives you working sample queries for it, and it bundles an agent skill by [Andrew Petrochuk](https://github.com/petrochuk) so [GitHub Copilot](https://github.com/features/copilot) can build and run them for you.

The skill lives in [`.github/skills/pac-admin-query/`](./.github/skills/pac-admin-query/) and bundles a `SKILL.md` reference plus ready-to-adapt example query files.

---

## ✅ Prerequisites

- A PAC CLI build that includes `pac admin query` (verified with `2.9.3`).
- An active PAC auth profile with tenant context and permission to call the [Power Platform inventory API](https://learn.microsoft.com/power-platform/admin/inventory-api).

```powershell
pac auth list      # inspect profiles
pac auth select    # activate the right one
```

---

## 🚀 Usage

> **Tip:** Prefer a query file over inline JSON to avoid shell-escaping headaches.

### 🎛️ Arguments

| Argument | Alias | Description |
| --- | --- | --- |
| `--query-file` | `-qf` | Read the query body from a `.json` file. |
| `--query` | `-q` | Provide the query body inline as a string. |
| `--output-type` | `-ot` | `Grid` *(default)*, `List`, or `Json`. |
| `--output-file` | `-of` | Write results to a file (`Grid` CSV, `Json` JSON). |

| Output type | Flag | Best for |
| --- | --- | --- |
| `Grid` *(default)* | `-ot Grid` | Quick, readable tables |
| `List` | `-ot List` | Simple line-per-field text |
| `Json` | `-ot Json` | Nested data, metadata & pagination |

### 🖥️ Command samples

```powershell
# Run a query file, print a Grid table to the console (default output type)
pac admin query --query-file .\query.json
pac admin query -qf .\query.json                             # same, using the alias

# Full JSON (metadata, nested props, pagination token) to the console
pac admin query --query-file .\query.json --output-type Json
pac admin query -qf .\query.json -ot Json                    # alias form

# Simple line-per-field List output
pac admin query -qf .\query.json -ot List

# Export a CSV file (Grid + --output-file)
pac admin query --query-file .\query.json --output-type Grid --output-file .\inventory.csv
pac admin query -qf .\query.json -ot Grid -of .\inventory.csv

# Export the raw JSON response to a file
pac admin query -qf .\query.json -ot Json -of .\inventory.json
```

> There's also an inline `--query` / `-q` flag, but shell quoting around the JSON (especially the `$type` keys) frequently trips it up. **Stick with `--query-file`** — save the JSON to a `.json` file and point `-qf` at it.

### 🔐 Pick the right auth profile first

```powershell
pac auth list                                                # see profiles
pac auth select --index 1                                    # activate one
pac auth who                                                 # confirm the active profile & tenant
```

### 📄 Paginating large result sets

`Grid`/`List` cap what they print. For big exports, use `Json` and follow the `skipToken`: when the response has a non-empty `skipToken`, copy it into `Options.SkipToken` in your query file and rerun to fetch the next page — keep every other clause unchanged.

```jsonc
{
  "TableName": "PowerPlatformResources",
  "Clauses": [ /* ... */ ],
  "Options": { "Top": 1000, "Skip": 0, "SkipToken": "<paste skipToken here>" }
}
```

See [`SKILL.md`](./.github/skills/pac-admin-query/SKILL.md) for the full request shape, clause reference, and resource-type table.

---

## 💡 Example queries

These governance-style examples are ones I validated against a live tenant.

> **Heads up:** The inventory API exposes resource *structure* (owner, environment, connectors, created/modified dates) — **not** run/launch telemetry or Entra account status. So treat "stale" as *not recently modified* rather than *unused*, and confirm "orphaned" ownership separately.

### 👷 Maker adoption — top makers by resource count

Who is building the most apps, flows, and agents across the tenant?

```json
{
  "TableName": "PowerPlatformResources",
  "Clauses": [
    {
      "$type": "where",
      "FieldName": "type",
      "Operator": "in~",
      "Values": [
        "'microsoft.powerapps/canvasapps'",
        "'microsoft.powerapps/modeldrivenapps'",
        "'microsoft.powerautomate/cloudflows'",
        "'microsoft.powerautomate/agentflows'",
        "'microsoft.copilotstudio/agents'"
      ]
    },
    { "$type": "extend", "FieldName": "Maker", "Expression": "tostring(properties.createdBy)" },
    {
      "$type": "summarize",
      "SummarizeClauseExpression": {
        "OperatorName": "count",
        "OperatorFieldName": "ResourceCount",
        "FieldList": ["Maker"]
      }
    },
    { "$type": "orderby", "FieldNamesAscDesc": { "ResourceCount": "desc" } }
  ]
}
```

### 🌍 Environment sprawl — resource count per environment and type

Break down how many resources of each type live in each environment.

```json
{
  "TableName": "PowerPlatformResources",
  "Clauses": [
    {
      "$type": "where",
      "FieldName": "type",
      "Operator": "in~",
      "Values": [
        "'microsoft.powerapps/canvasapps'",
        "'microsoft.powerautomate/cloudflows'",
        "'microsoft.powerautomate/agentflows'",
        "'microsoft.copilotstudio/agents'"
      ]
    },
    { "$type": "extend", "FieldName": "EnvId", "Expression": "tostring(properties.environmentId)" },
    {
      "$type": "summarize",
      "SummarizeClauseExpression": {
        "OperatorName": "count",
        "OperatorFieldName": "ResourceCount",
        "FieldList": ["EnvId", "type"]
      }
    },
    { "$type": "orderby", "FieldNamesAscDesc": { "ResourceCount": "desc" } }
  ]
}
```

### 🔌 Connector audit — rank connectors by usage

Aggregate every connector used across the tenant to spot premium or risky connectors. Uses `mvexpand` + `summarize` (empirical clauses — see [`SKILL.md`](./.github/skills/pac-admin-query/SKILL.md)).

```json
{
  "TableName": "PowerPlatformResources",
  "Clauses": [
    {
      "$type": "where",
      "FieldName": "type",
      "Operator": "in~",
      "Values": [
        "'microsoft.powerapps/canvasapps'",
        "'microsoft.powerautomate/cloudflows'",
        "'microsoft.powerautomate/agentflows'",
        "'microsoft.copilotstudio/agents'"
      ]
    },
    { "$type": "extend", "FieldName": "ppc", "Expression": "properties.powerPlatformConnectors" },
    { "$type": "mvexpand", "FieldList": ["ppc"] },
    { "$type": "extend", "FieldName": "ConnectorId", "Expression": "tostring(ppc.connectorId)" },
    { "$type": "where", "FieldName": "ConnectorId", "Operator": "!=", "Values": ["''"] },
    {
      "$type": "summarize",
      "SummarizeClauseExpression": {
        "OperatorName": "count",
        "OperatorFieldName": "UsageCount",
        "FieldList": ["ConnectorId"]
      }
    },
    { "$type": "orderby", "FieldNamesAscDesc": { "UsageCount": "desc" } }
  ]
}
```

### 🆕 New resources — created in the last 30 days

Track growth and see what's been built lately.

```json
{
  "TableName": "PowerPlatformResources",
  "Clauses": [
    {
      "$type": "where",
      "FieldName": "type",
      "Operator": "in~",
      "Values": [
        "'microsoft.powerapps/canvasapps'",
        "'microsoft.powerautomate/cloudflows'",
        "'microsoft.powerautomate/agentflows'",
        "'microsoft.copilotstudio/agents'"
      ]
    },
    { "$type": "extend", "FieldName": "Created", "Expression": "todatetime(properties.createdAt)" },
    { "$type": "where", "FieldName": "Created", "Operator": ">=", "Values": ["ago(30d)"] },
    {
      "$type": "project",
      "FieldList": [
        "Name = properties.displayName",
        "Type = type",
        "Created",
        "Owner = properties.ownerId"
      ]
    },
    { "$type": "orderby", "FieldNamesAscDesc": { "Created": "desc" } }
  ]
}
```

### 📈 Adoption trend — resources created per month

Bin every app, flow, and agent by the month it was created to see how fast the tenant is growing over time.

```json
{
  "TableName": "PowerPlatformResources",
  "Clauses": [
    {
      "$type": "where",
      "FieldName": "type",
      "Operator": "in~",
      "Values": [
        "'microsoft.powerapps/canvasapps'",
        "'microsoft.powerautomate/cloudflows'",
        "'microsoft.powerautomate/agentflows'",
        "'microsoft.copilotstudio/agents'"
      ]
    },
    { "$type": "extend", "FieldName": "Month", "Expression": "startofmonth(todatetime(properties.createdAt))" },
    {
      "$type": "summarize",
      "SummarizeClauseExpression": {
        "OperatorName": "count",
        "OperatorFieldName": "ResourceCount",
        "FieldList": ["Month"]
      }
    },
    { "$type": "orderby", "FieldNamesAscDesc": { "Month": "asc" } }
  ]
}
```

### ✏️ Recently modified — changed in the last 7 days

Watch for churn. This surfaces everything touched in the past week, newest first, which is handy for spotting active development or unexpected changes.

```json
{
  "TableName": "PowerPlatformResources",
  "Clauses": [
    {
      "$type": "where",
      "FieldName": "type",
      "Operator": "in~",
      "Values": [
        "'microsoft.powerapps/canvasapps'",
        "'microsoft.powerautomate/cloudflows'",
        "'microsoft.powerautomate/agentflows'",
        "'microsoft.copilotstudio/agents'"
      ]
    },
    { "$type": "extend", "FieldName": "Modified", "Expression": "todatetime(properties.lastModifiedAt)" },
    { "$type": "where", "FieldName": "Modified", "Operator": ">=", "Values": ["ago(7d)"] },
    { "$type": "project", "FieldList": ["Name = properties.displayName", "Type = type", "Modified"] },
    { "$type": "orderby", "FieldNamesAscDesc": { "Modified": "desc" } }
  ]
}
```

### 🛡️ Managed environments — managed vs unmanaged

Count how many environments are Managed Environments versus not.

```json
{
  "TableName": "PowerPlatformResources",
  "Clauses": [
    { "$type": "where", "FieldName": "type", "Operator": "==", "Values": ["'microsoft.powerplatform/environments'"] },
    { "$type": "extend", "FieldName": "IsManaged", "Expression": "tostring(properties.isManaged)" },
    {
      "$type": "summarize",
      "SummarizeClauseExpression": {
        "OperatorName": "count",
        "OperatorFieldName": "EnvironmentCount",
        "FieldList": ["IsManaged"]
      }
    },
    { "$type": "orderby", "FieldNamesAscDesc": { "EnvironmentCount": "desc" } }
  ]
}
```

### 🏷️ Environment types — production, sandbox, developer, and more

Break your environments down by type. A pile of Developer environments usually means everyone spun up their own, which is worth knowing before you tighten governance.

```json
{
  "TableName": "PowerPlatformResources",
  "Clauses": [
    { "$type": "where", "FieldName": "type", "Operator": "==", "Values": ["'microsoft.powerplatform/environments'"] },
    { "$type": "extend", "FieldName": "EnvironmentType", "Expression": "tostring(properties.environmentType)" },
    {
      "$type": "summarize",
      "SummarizeClauseExpression": {
        "OperatorName": "count",
        "OperatorFieldName": "EnvironmentCount",
        "FieldList": ["EnvironmentType"]
      }
    },
    { "$type": "orderby", "FieldNamesAscDesc": { "EnvironmentCount": "desc" } }
  ]
}
```

### 🌐 Environments by region — where your data lives

Count environments per region. On a single-geo tenant this is one row, but if you operate across geographies it's a quick data-residency check.

```json
{
  "TableName": "PowerPlatformResources",
  "Clauses": [
    { "$type": "where", "FieldName": "type", "Operator": "==", "Values": ["'microsoft.powerplatform/environments'"] },
    { "$type": "extend", "FieldName": "Region", "Expression": "tostring(location)" },
    {
      "$type": "summarize",
      "SummarizeClauseExpression": {
        "OperatorName": "count",
        "OperatorFieldName": "EnvironmentCount",
        "FieldList": ["Region"]
      }
    },
    { "$type": "orderby", "FieldNamesAscDesc": { "EnvironmentCount": "desc" } }
  ]
}
```

### 📆 Recently used resources — activity by last used

The tenant also exposes a `microsoft.powerplatformusage/usagerecords` type carrying `properties.lastUsed` and `properties.resourceId` — **real usage telemetry**. Join it back to the resource to see the friendly name, newest activity first.

```json
{
  "TableName": "PowerPlatformResources",
  "Clauses": [
    { "$type": "where", "FieldName": "type", "Operator": "==", "Values": ["'microsoft.powerplatformusage/usagerecords'"] },
    { "$type": "extend", "FieldName": "LastUsed", "Expression": "todatetime(properties.lastUsed)" },
    { "$type": "extend", "FieldName": "joinKey", "Expression": "tolower(tostring(properties.resourceId))" },
    {
      "$type": "join",
      "JoinKind": "leftouter",
      "RightTable": {
        "TableName": "PowerPlatformResources",
        "Clauses": [
          {
            "$type": "project",
            "FieldList": [
              "joinKey = tolower(name)",
              "ResourceName = properties.displayName",
              "ResourceType = type"
            ]
          }
        ]
      },
      "LeftColumnName": "joinKey",
      "RightColumnName": "joinKey"
    },
    { "$type": "project", "FieldList": ["ResourceName", "ResourceType", "LastUsed"] },
    { "$type": "orderby", "FieldNamesAscDesc": { "LastUsed": "desc" } }
  ]
}
```

### 🧹 Unused resources — not used in 30+ days

Flip the usage query around: surface resources whose last activity is older than 30 days. These are cleanup candidates based on real usage rather than just modified dates.

```json
{
  "TableName": "PowerPlatformResources",
  "Clauses": [
    { "$type": "where", "FieldName": "type", "Operator": "==", "Values": ["'microsoft.powerplatformusage/usagerecords'"] },
    { "$type": "extend", "FieldName": "LastUsed", "Expression": "todatetime(properties.lastUsed)" },
    { "$type": "where", "FieldName": "LastUsed", "Operator": "<", "Values": ["ago(30d)"] },
    { "$type": "extend", "FieldName": "joinKey", "Expression": "tolower(tostring(properties.resourceId))" },
    {
      "$type": "join",
      "JoinKind": "leftouter",
      "RightTable": {
        "TableName": "PowerPlatformResources",
        "Clauses": [
          {
            "$type": "project",
            "FieldList": [
              "joinKey = tolower(name)",
              "ResourceName = properties.displayName",
              "ResourceType = type",
              "Owner = properties.ownerId"
            ]
          }
        ]
      },
      "LeftColumnName": "joinKey",
      "RightColumnName": "joinKey"
    },
    { "$type": "project", "FieldList": ["ResourceName", "ResourceType", "LastUsed", "Owner"] },
    { "$type": "orderby", "FieldNamesAscDesc": { "LastUsed": "asc" } }
  ]
}
```

### 🧑‍🦲 Orphaned resources — owners to cross-check against deleted accounts

The inventory API gives you each resource's owner ID, but not whether that account still exists in Entra ID. So you can't ask it for "orphaned" resources directly. What you *can* do is group everything by owner and export the list, then diff those owner IDs against your disabled or deleted accounts. Anything owned by an account that's gone is orphaned.

```json
{
  "TableName": "PowerPlatformResources",
  "Clauses": [
    {
      "$type": "where",
      "FieldName": "type",
      "Operator": "in~",
      "Values": [
        "'microsoft.powerapps/canvasapps'",
        "'microsoft.powerapps/modeldrivenapps'",
        "'microsoft.powerautomate/cloudflows'",
        "'microsoft.powerautomate/agentflows'",
        "'microsoft.copilotstudio/agents'"
      ]
    },
    { "$type": "extend", "FieldName": "Owner", "Expression": "tostring(properties.ownerId)" },
    { "$type": "where", "FieldName": "Owner", "Operator": "!=", "Values": ["''"] },
    {
      "$type": "summarize",
      "SummarizeClauseExpression": {
        "OperatorName": "count",
        "OperatorFieldName": "ResourceCount",
        "FieldList": ["Owner"]
      }
    },
    { "$type": "orderby", "FieldNamesAscDesc": { "ResourceCount": "desc" } }
  ]
}
```

### 🧯 Stale connectors — connectors whose usage has gone cold

> **Note:** The inventory API doesn't expose individual **connection** instances, so you can't query stale *connections* directly. As an approximation, this ranks each **connector** by the most recent activity of any resource that uses it — a `null` (or old) `LastUsed` flags connectors only referenced by dormant resources. Uses `mvexpand` + `argmax` (empirical — see [`SKILL.md`](./.github/skills/pac-admin-query/SKILL.md)).

```json
{
  "TableName": "PowerPlatformResources",
  "Clauses": [
    {
      "$type": "where",
      "FieldName": "type",
      "Operator": "in~",
      "Values": [
        "'microsoft.powerapps/canvasapps'",
        "'microsoft.powerautomate/cloudflows'",
        "'microsoft.powerautomate/agentflows'",
        "'microsoft.copilotstudio/agents'"
      ]
    },
    { "$type": "extend", "FieldName": "joinKey", "Expression": "tolower(tostring(name))" },
    { "$type": "extend", "FieldName": "ppc", "Expression": "properties.powerPlatformConnectors" },
    { "$type": "mvexpand", "FieldList": ["ppc"] },
    { "$type": "extend", "FieldName": "ConnectorId", "Expression": "tostring(ppc.connectorId)" },
    { "$type": "where", "FieldName": "ConnectorId", "Operator": "!=", "Values": ["''"] },
    {
      "$type": "join",
      "JoinKind": "leftouter",
      "RightTable": {
        "TableName": "PowerPlatformResources",
        "Clauses": [
          { "$type": "where", "FieldName": "type", "Operator": "==", "Values": ["'microsoft.powerplatformusage/usagerecords'"] },
          {
            "$type": "project",
            "FieldList": [
              "joinKey = tolower(tostring(properties.resourceId))",
              "LastUsed = todatetime(properties.lastUsed)"
            ]
          }
        ]
      },
      "LeftColumnName": "joinKey",
      "RightColumnName": "joinKey"
    },
    {
      "$type": "summarize",
      "SummarizeClauseExpression": {
        "OperatorName": "argmax",
        "OperatorFieldName": "LastUsed",
        "FieldList": ["ConnectorId"]
      }
    },
    { "$type": "project", "FieldList": ["ConnectorId", "LastUsed"] },
    { "$type": "orderby", "FieldNamesAscDesc": { "LastUsed": "asc" } }
  ]
}
```

---

## 📚 References

- [Power Platform inventory API](https://learn.microsoft.com/power-platform/admin/inventory-api)
- [Power Platform inventory schema](https://learn.microsoft.com/power-platform/admin/inventory-schema)
