# 🧯 Stale connectors

Rank each connector by the most recent activity of any resource that uses it. A `null` or old `LastUsed` flags connectors only referenced by dormant resources.

> [!NOTE]
> The inventory API doesn't expose individual connection instances, so you can't query stale connections directly. This ranks connectors as an approximation. It uses `mvexpand` and `argmax`, which are empirical (see [`SKILL.md`](../../.github/skills/pac-admin-query/SKILL.md)).

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
    {
      "$type": "extend",
      "FieldName": "joinKey",
      "Expression": "tolower(tostring(name))"
    },
    {
      "$type": "extend",
      "FieldName": "ppc",
      "Expression": "properties.powerPlatformConnectors"
    },
    {
      "$type": "mvexpand",
      "FieldList": [
        "ppc"
      ]
    },
    {
      "$type": "extend",
      "FieldName": "ConnectorId",
      "Expression": "tostring(ppc.connectorId)"
    },
    {
      "$type": "where",
      "FieldName": "ConnectorId",
      "Operator": "!=",
      "Values": [
        "''"
      ]
    },
    {
      "$type": "join",
      "JoinKind": "leftouter",
      "RightTable": {
        "TableName": "PowerPlatformResources",
        "Clauses": [
          {
            "$type": "where",
            "FieldName": "type",
            "Operator": "==",
            "Values": [
              "'microsoft.powerplatformusage/usagerecords'"
            ]
          },
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
        "FieldList": [
          "ConnectorId"
        ]
      }
    },
    {
      "$type": "project",
      "FieldList": [
        "ConnectorId",
        "LastUsed"
      ]
    },
    {
      "$type": "orderby",
      "FieldNamesAscDesc": {
        "LastUsed": "asc"
      }
    }
  ]
}
```

[download](https://raw.githubusercontent.com/Laskewitz/pac-admin-query/main/samples/stale-connectors/query.json)

## Run it

```bash
pac admin query --query-file samples/stale-connectors/query.json --output-type Json
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
      "ConnectorId": "shared_rss",
      "LastUsed": null
    },
    {
      "ConnectorId": "shared_customconnector1",
      "LastUsed": null
    },
    {
      "ConnectorId": "shared_wordonlinebusiness",
      "LastUsed": "2026-06-24T23:59:59Z"
    },
    {
      "ConnectorId": "shared_advancedapprovals",
      "LastUsed": "2026-06-25T23:59:59Z"
    },
    {
      "ConnectorId": "shared_msnweather",
      "LastUsed": "2026-06-28T23:59:59Z"
    },
    {
      "ConnectorId": "shared_a365copilotchatmcp",
      "LastUsed": "2026-06-30T23:59:59Z"
    },
    {
      "ConnectorId": "shared_a365memcp",
      "LastUsed": "2026-06-30T23:59:59Z"
    },
    {
      "ConnectorId": "shared_powertools",
      "LastUsed": "2026-07-09T23:59:59Z"
    },
    {
      "ConnectorId": "shared_sharepointonline",
      "LastUsed": "2026-07-09T23:59:59Z"
    },
    {
      "ConnectorId": "shared_agentnode",
      "LastUsed": "2026-07-10T23:59:59Z"
    },
    {
      "ConnectorId": "shared_commondataserviceforapps",
      "LastUsed": "2026-07-10T23:59:59Z"
    },
    {
      "ConnectorId": "shared_office365",
      "LastUsed": "2026-07-10T23:59:59Z"
    },
    {
      "ConnectorId": "shared_uiflow",
      "LastUsed": "2026-07-10T23:59:59Z"
    },
    {
      "ConnectorId": "shared_microsoftcopilotstudio",
      "LastUsed": "2026-07-10T23:59:59Z"
    },
    {
      "ConnectorId": "shared_onenote",
      "LastUsed": "2026-07-10T23:59:59Z"
    },
    {
      "ConnectorId": "shared_teams",
      "LastUsed": "2026-07-10T23:59:59Z"
    },
    {
      "ConnectorId": "shared_computeroperator",
      "LastUsed": "2026-07-10T23:59:59Z"
    }
  ],
  "facets": []
}
```

[download](https://raw.githubusercontent.com/Laskewitz/pac-admin-query/main/samples/stale-connectors/output.json)
