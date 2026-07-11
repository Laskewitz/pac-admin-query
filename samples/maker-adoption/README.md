# 👷 Maker adoption

Who is building the most apps, flows, and agents across the tenant? This groups every resource by its creator and counts what each person owns, so the busiest makers rise to the top.

## Query

```json
{
  "TableName": "PowerPlatformResources",
  "Clauses": [
    { "$type": "where", "FieldName": "type", "Operator": "in~", "Values": [ "'microsoft.powerapps/canvasapps'", "'microsoft.powerapps/modeldrivenapps'", "'microsoft.powerautomate/cloudflows'", "'microsoft.powerautomate/agentflows'", "'microsoft.copilotstudio/agents'" ] },
    { "$type": "extend", "FieldName": "Maker", "Expression": "tostring(properties.createdBy)" },
    { "$type": "summarize", "SummarizeClauseExpression": { "OperatorName": "count", "OperatorFieldName": "ResourceCount", "FieldList": ["Maker"] } },
    { "$type": "orderby", "FieldNamesAscDesc": { "ResourceCount": "desc" } }
  ]
}
```

## Run it

```bash
pac admin query --query-file samples/maker-adoption/query.json --output-type Json
```

## Sample output

Sanitized excerpt from a live tenant.

```json
{
  "totalRecords": 6,
  "count": 6,
  "resultTruncated": 1,
  "skipToken": null,
  "data": [
    {
      "Maker": "aaaaaaaa-bbbb-cccc-dddd-000000000001",
      "ResourceCount": 414
    },
    {
      "Maker": "aaaaaaaa-bbbb-cccc-dddd-000000000002",
      "ResourceCount": 57
    },
    {
      "Maker": "aaaaaaaa-bbbb-cccc-dddd-000000000003",
      "ResourceCount": 33
    },
    {
      "Maker": "aaaaaaaa-bbbb-cccc-dddd-000000000004",
      "ResourceCount": 29
    },
    {
      "Maker": "aaaaaaaa-bbbb-cccc-dddd-000000000005",
      "ResourceCount": 24
    },
    {
      "Maker": "aaaaaaaa-bbbb-cccc-dddd-000000000006",
      "ResourceCount": 3
    }
  ],
  "facets": []
}
```
