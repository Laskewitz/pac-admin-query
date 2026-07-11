# 🔌 Connectors per environment

See which connectors are used in each environment and how often. This expands every resource's connector list and groups the result by environment and connector, so you can tell which environment leans on a given connector before you scope a DLP policy.

> [!NOTE]
> Output is truncated to 20 of 39 rows. `totalRecords` shows the real count. Uses `mvexpand` and `summarize`, which are empirical (see [`SKILL.md`](../../.github/skills/pac-admin-query/SKILL.md)).

## Query

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
    { "$type": "extend", "FieldName": "ppc", "Expression": "properties.powerPlatformConnectors" },
    { "$type": "mvexpand", "FieldList": ["ppc"] },
    { "$type": "extend", "FieldName": "ConnectorId", "Expression": "tostring(ppc.connectorId)" },
    { "$type": "where", "FieldName": "ConnectorId", "Operator": "!=", "Values": ["''"] },
    {
      "$type": "summarize",
      "SummarizeClauseExpression": {
        "OperatorName": "count",
        "OperatorFieldName": "UsageCount",
        "FieldList": ["EnvId", "ConnectorId"]
      }
    },
    { "$type": "orderby", "FieldNamesAscDesc": { "UsageCount": "desc" } }
  ]
}
```

[Download](https://raw.githubusercontent.com/Laskewitz/pac-admin-query/main/samples/connectors-per-environment/query.json)

## Run it

```bash
pac admin query --query-file samples/connectors-per-environment/query.json --output-type Json
```

## Sample output

Sanitized excerpt from a live tenant.

```json
{
  "totalRecords": 39,
  "count": 20,
  "resultTruncated": 1,
  "skipToken": null,
  "data": [
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000001",
      "ConnectorId": "shared_agentnode",
      "UsageCount": 382
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000001",
      "ConnectorId": "shared_advancedapprovals",
      "UsageCount": 22
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000002",
      "ConnectorId": "shared_commondataserviceforapps",
      "UsageCount": 20
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000003",
      "ConnectorId": "shared_computeroperator",
      "UsageCount": 12
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000004",
      "ConnectorId": "shared_commondataserviceforapps",
      "UsageCount": 8
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000001",
      "ConnectorId": "shared_commondataserviceforapps",
      "UsageCount": 8
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000004",
      "ConnectorId": "shared_teams",
      "UsageCount": 5
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000004",
      "ConnectorId": "shared_office365",
      "UsageCount": 4
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000003",
      "ConnectorId": "shared_office365",
      "UsageCount": 4
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000004",
      "ConnectorId": "shared_agentnode",
      "UsageCount": 4
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000005",
      "ConnectorId": "shared_office365",
      "UsageCount": 4
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000003",
      "ConnectorId": "shared_commondataserviceforapps",
      "UsageCount": 3
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000005",
      "ConnectorId": "shared_computeroperator",
      "UsageCount": 3
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000003",
      "ConnectorId": "shared_microsoftcopilotstudio",
      "UsageCount": 3
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000005",
      "ConnectorId": "shared_sharepointonline",
      "UsageCount": 3
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000006",
      "ConnectorId": "shared_sharepointonline",
      "UsageCount": 2
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000006",
      "ConnectorId": "shared_office365",
      "UsageCount": 2
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000007",
      "ConnectorId": "shared_office365",
      "UsageCount": 2
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000007",
      "ConnectorId": "shared_sharepointonline",
      "UsageCount": 2
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000001",
      "ConnectorId": "shared_microsoftcopilotstudio",
      "UsageCount": 2
    }
  ],
  "facets": []
}
```

[Download](https://raw.githubusercontent.com/Laskewitz/pac-admin-query/main/samples/connectors-per-environment/output.json)
