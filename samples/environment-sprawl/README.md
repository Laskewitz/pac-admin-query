# 🌍 Environment sprawl

Break down how many resources of each type live in each environment. Useful for spotting the one environment that quietly turned into everyone's dumping ground.

> [!NOTE]
> Output is truncated to 20 of 24 rows. `totalRecords` shows the real count.

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
      "FieldName": "EnvId",
      "Expression": "tostring(properties.environmentId)"
    },
    {
      "$type": "summarize",
      "SummarizeClauseExpression": {
        "OperatorName": "count",
        "OperatorFieldName": "ResourceCount",
        "FieldList": [
          "EnvId",
          "type"
        ]
      }
    },
    {
      "$type": "orderby",
      "FieldNamesAscDesc": {
        "ResourceCount": "desc"
      }
    }
  ]
}
```

## Run it

```bash
pac admin query --query-file samples/environment-sprawl/query.json --output-type Json
```

## Sample output

Sanitized excerpt from a live tenant.

```json
{
  "totalRecords": 24,
  "count": 20,
  "resultTruncated": 1,
  "skipToken": null,
  "data": [
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000001",
      "type": "microsoft.powerautomate/cloudflows",
      "ResourceCount": 195
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000001",
      "type": "microsoft.powerautomate/agentflows",
      "ResourceCount": 191
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000002",
      "type": "microsoft.powerautomate/cloudflows",
      "ResourceCount": 30
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000003",
      "type": "microsoft.powerautomate/agentflows",
      "ResourceCount": 11
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000001",
      "type": "microsoft.copilotstudio/agents",
      "ResourceCount": 9
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000004",
      "type": "microsoft.powerautomate/agentflows",
      "ResourceCount": 9
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000005",
      "type": "microsoft.copilotstudio/agents",
      "ResourceCount": 7
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000003",
      "type": "microsoft.copilotstudio/agents",
      "ResourceCount": 6
    },
    {
      "EnvId": "default-aaaaaaaa-bbbb-cccc-dddd-000000000006",
      "type": "microsoft.copilotstudio/agents",
      "ResourceCount": 5
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000004",
      "type": "microsoft.copilotstudio/agents",
      "ResourceCount": 4
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000007",
      "type": "microsoft.copilotstudio/agents",
      "ResourceCount": 4
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000008",
      "type": "microsoft.copilotstudio/agents",
      "ResourceCount": 3
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000007",
      "type": "microsoft.powerautomate/cloudflows",
      "ResourceCount": 3
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000009",
      "type": "microsoft.copilotstudio/agents",
      "ResourceCount": 3
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000008",
      "type": "microsoft.powerautomate/agentflows",
      "ResourceCount": 2
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000010",
      "type": "microsoft.powerautomate/agentflows",
      "ResourceCount": 2
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000003",
      "type": "microsoft.powerautomate/cloudflows",
      "ResourceCount": 2
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000002",
      "type": "microsoft.copilotstudio/agents",
      "ResourceCount": 2
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000011",
      "type": "microsoft.copilotstudio/agents",
      "ResourceCount": 1
    },
    {
      "EnvId": "aaaaaaaa-bbbb-cccc-dddd-000000000005",
      "type": "microsoft.powerautomate/agentflows",
      "ResourceCount": 1
    }
  ],
  "facets": []
}
```
