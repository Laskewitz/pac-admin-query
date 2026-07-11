# 🧑‍🦲 Orphaned resources

The inventory API gives you each resource's owner ID, but not whether that account still exists in Entra ID, so you can't ask it for orphaned resources directly. Group everything by owner, export the list, then diff those owner IDs against your disabled or deleted accounts. Anything owned by an account that's gone is orphaned.

> [!IMPORTANT]
> The API can't see Entra account status. Confirm ownership against your directory before treating anything as orphaned.

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

## Run it

```bash
pac admin query --query-file samples/orphaned-resources/query.json --output-type Json
```

## Sample output

Sanitized excerpt from a live tenant.

```json
{
  "totalRecords": 5,
  "count": 5,
  "resultTruncated": 1,
  "skipToken": null,
  "data": [
    {
      "Owner": "aaaaaaaa-bbbb-cccc-dddd-000000000001",
      "ResourceCount": 410
    },
    {
      "Owner": "aaaaaaaa-bbbb-cccc-dddd-000000000002",
      "ResourceCount": 32
    },
    {
      "Owner": "aaaaaaaa-bbbb-cccc-dddd-000000000003",
      "ResourceCount": 29
    },
    {
      "Owner": "aaaaaaaa-bbbb-cccc-dddd-000000000004",
      "ResourceCount": 20
    },
    {
      "Owner": "aaaaaaaa-bbbb-cccc-dddd-000000000005",
      "ResourceCount": 3
    }
  ],
  "facets": []
}
```
