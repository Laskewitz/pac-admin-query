# 🔌 Connector audit

Aggregate every connector used across the tenant so premium or risky connectors are easy to spot. This one leans on `mvexpand` and `summarize`, which aren't documented but work in practice (see [`SKILL.md`](../../.github/skills/pac-admin-query/SKILL.md)).

## Query

```json
{
  "TableName": "PowerPlatformResources",
  "Clauses": [
    { "$type": "where", "FieldName": "type", "Operator": "in~", "Values": [ "'microsoft.powerapps/canvasapps'", "'microsoft.powerautomate/cloudflows'", "'microsoft.powerautomate/agentflows'", "'microsoft.copilotstudio/agents'" ] },
    { "$type": "extend", "FieldName": "ppc", "Expression": "properties.powerPlatformConnectors" },
    { "$type": "mvexpand", "FieldList": ["ppc"] },
    { "$type": "extend", "FieldName": "ConnectorId", "Expression": "tostring(ppc.connectorId)" },
    { "$type": "where", "FieldName": "ConnectorId", "Operator": "!=", "Values": ["''"] },
    { "$type": "summarize", "SummarizeClauseExpression": { "OperatorName": "count", "OperatorFieldName": "UsageCount", "FieldList": ["ConnectorId"] } },
    { "$type": "orderby", "FieldNamesAscDesc": { "UsageCount": "desc" } }
  ]
}
```

[Download](https://raw.githubusercontent.com/Laskewitz/pac-admin-query/main/samples/connector-audit/query.json)

## Run it

```bash
pac admin query --query-file samples/connector-audit/query.json --output-type Json
```

## Sample output

Sanitized excerpt from a live tenant.

```json
{
  "totalRecords": 17,
  "count": 17,
  "resultTruncated": 1,
  "skipToken": null,
  "data": [
    {
      "ConnectorId": "shared_agentnode",
      "UsageCount": 386
    },
    {
      "ConnectorId": "shared_commondataserviceforapps",
      "UsageCount": 42
    },
    {
      "ConnectorId": "shared_advancedapprovals",
      "UsageCount": 22
    },
    {
      "ConnectorId": "shared_office365",
      "UsageCount": 18
    },
    {
      "ConnectorId": "shared_computeroperator",
      "UsageCount": 17
    },
    {
      "ConnectorId": "shared_sharepointonline",
      "UsageCount": 8
    },
    {
      "ConnectorId": "shared_microsoftcopilotstudio",
      "UsageCount": 7
    },
    {
      "ConnectorId": "shared_teams",
      "UsageCount": 5
    },
    {
      "ConnectorId": "shared_msnweather",
      "UsageCount": 3
    },
    {
      "ConnectorId": "shared_powertools",
      "UsageCount": 2
    },
    {
      "ConnectorId": "shared_customconnector1",
      "UsageCount": 2
    },
    {
      "ConnectorId": "shared_onenote",
      "UsageCount": 2
    },
    {
      "ConnectorId": "shared_a365memcp",
      "UsageCount": 1
    },
    {
      "ConnectorId": "shared_rss",
      "UsageCount": 1
    },
    {
      "ConnectorId": "shared_a365copilotchatmcp",
      "UsageCount": 1
    },
    {
      "ConnectorId": "shared_wordonlinebusiness",
      "UsageCount": 1
    },
    {
      "ConnectorId": "shared_uiflow",
      "UsageCount": 1
    }
  ],
  "facets": []
}
```

[Download](https://raw.githubusercontent.com/Laskewitz/pac-admin-query/main/samples/connector-audit/output.json)
