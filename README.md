# 🔍 pac-admin-query

> A [GitHub Copilot](https://github.com/features/copilot) agent **skill** for building and running **Power Platform inventory** queries with `pac admin query`.

List, filter, count, aggregate, export, and inspect tenant-wide **Power Apps** 🎨, **Power Automate flows** ⚡, **Copilot Studio agents** 🤖, **environments** 🌍, **environment groups** 🗂️, and **connectors** 🔌 — all from the command line.

📦 The skill lives in [`.github/skills/pac-admin-query/`](./.github/skills/pac-admin-query/) and bundles a `SKILL.md` reference plus ready-to-adapt example query files.

---

## 📑 Contents

- [✅ Prerequisites](#-prerequisites)
- [🚀 Usage](#-usage)
- [💡 Example queries](#-example-queries)
  - [👷 Maker adoption](#-maker-adoption--top-makers-by-resource-count)
  - [🌍 Environment sprawl](#-environment-sprawl--resource-count-per-environment-and-type)
  - [🔌 Connector audit](#-connector-audit--rank-connectors-by-usage)
- [📚 References](#-references)

---

## ✅ Prerequisites

- 🛠️ A PAC CLI build that includes `pac admin query` (verified with `2.9.3`).
- 🔐 An active PAC auth profile with tenant context and permission to call the [Power Platform inventory API](https://learn.microsoft.com/en-us/power-platform/admin/inventory-api).

```powershell
pac auth list      # 👀 inspect profiles
pac auth select    # 🎯 activate the right one
```

---

## 🚀 Usage

> 💡 **Tip:** Prefer a query file over inline JSON to avoid shell-escaping headaches.

```powershell
pac admin query --query-file .\query.json                    # 📋 Grid (CSV-like) to console
pac admin query -qf .\query.json -ot Json -of .\out.json     # 🧾 full JSON with metadata
pac admin query -qf .\query.json -ot Grid -of .\out.csv      # 📈 CSV export
```

| Output type | Flag | Best for |
| --- | --- | --- |
| `Grid` *(default)* | `-ot Grid` | 👓 Quick, readable tables |
| `List` | `-ot List` | 📝 Simple line-per-field text |
| `Json` | `-ot Json` | 🧬 Nested data, metadata & pagination |

📖 See [`SKILL.md`](./.github/skills/pac-admin-query/SKILL.md) for the full request shape, clause reference, and resource-type table.

---

## 💡 Example queries

These governance-style examples were ✅ validated against a live tenant.

> ⚠️ **Heads up:** The inventory API exposes resource *structure* (owner, environment, connectors, created/modified dates) — **not** run/launch telemetry or Entra account status. So treat "stale" as *not recently modified* rather than *unused*, and confirm "orphaned" ownership separately.

### 👷 Maker adoption — top makers by resource count

Who is building the most apps, flows, and agents across the tenant? 🏆

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

Break down how many resources of each type live in each environment. 📊

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

Aggregate every connector used across the tenant to spot premium or risky connectors. 🚨 Uses `mvexpand` + `summarize` (empirical clauses — see [`SKILL.md`](./.github/skills/pac-admin-query/SKILL.md)).

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

---

## 📚 References

- 🌐 [Power Platform inventory API](https://learn.microsoft.com/en-us/power-platform/admin/inventory-api)
- 🧬 [Power Platform inventory schema](https://learn.microsoft.com/en-us/power-platform/admin/inventory-schema)
