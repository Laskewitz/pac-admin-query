# 🆕 New resources

Track growth by listing everything created in the last 30 days, newest first.

> [!NOTE]
> Output is truncated to 20 of 474 rows. `totalRecords` shows the real count.

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
      "FieldName": "Created",
      "Expression": "todatetime(properties.createdAt)"
    },
    {
      "$type": "where",
      "FieldName": "Created",
      "Operator": ">=",
      "Values": [
        "ago(30d)"
      ]
    },
    {
      "$type": "project",
      "FieldList": [
        "Name = properties.displayName",
        "Type = type",
        "Created",
        "Owner = properties.ownerId"
      ]
    },
    {
      "$type": "orderby",
      "FieldNamesAscDesc": {
        "Created": "desc"
      }
    }
  ]
}
```

[`query.json` (raw)](https://raw.githubusercontent.com/Laskewitz/pac-admin-query/main/samples/new-resources/query.json)

## Run it

```bash
pac admin query --query-file samples/new-resources/query.json --output-type Json
```

## Sample output

Sanitized excerpt from a live tenant.

```json
{
  "totalRecords": 474,
  "count": 20,
  "resultTruncated": 1,
  "skipToken": null,
  "data": [
    {
      "Name": "Sample resource 1",
      "Type": "microsoft.powerautomate/cloudflows",
      "Created": "2026-07-11T16:07:40Z",
      "Owner": "aaaaaaaa-bbbb-cccc-dddd-000000000015"
    },
    {
      "Name": "Sample resource 1",
      "Type": "microsoft.powerautomate/agentflows",
      "Created": "2026-07-11T16:07:39Z",
      "Owner": "aaaaaaaa-bbbb-cccc-dddd-000000000015"
    },
    {
      "Name": "Sample resource 1",
      "Type": "microsoft.powerautomate/cloudflows",
      "Created": "2026-07-11T16:07:18Z",
      "Owner": "aaaaaaaa-bbbb-cccc-dddd-000000000015"
    },
    {
      "Name": "Sample resource 1",
      "Type": "microsoft.powerautomate/agentflows",
      "Created": "2026-07-11T16:07:16Z",
      "Owner": "aaaaaaaa-bbbb-cccc-dddd-000000000015"
    },
    {
      "Name": "Sample resource 1",
      "Type": "microsoft.powerautomate/cloudflows",
      "Created": "2026-07-11T16:06:54Z",
      "Owner": "aaaaaaaa-bbbb-cccc-dddd-000000000015"
    },
    {
      "Name": "Sample resource 1",
      "Type": "microsoft.powerautomate/agentflows",
      "Created": "2026-07-11T16:06:52Z",
      "Owner": "aaaaaaaa-bbbb-cccc-dddd-000000000015"
    },
    {
      "Name": "Sample resource 2",
      "Type": "microsoft.powerautomate/cloudflows",
      "Created": "2026-07-11T15:56:58Z",
      "Owner": "aaaaaaaa-bbbb-cccc-dddd-000000000015"
    },
    {
      "Name": "Sample resource 2",
      "Type": "microsoft.powerautomate/agentflows",
      "Created": "2026-07-11T15:56:56Z",
      "Owner": "aaaaaaaa-bbbb-cccc-dddd-000000000015"
    },
    {
      "Name": "Sample resource 2",
      "Type": "microsoft.powerautomate/cloudflows",
      "Created": "2026-07-11T15:56:33Z",
      "Owner": "aaaaaaaa-bbbb-cccc-dddd-000000000015"
    },
    {
      "Name": "Sample resource 2",
      "Type": "microsoft.powerautomate/agentflows",
      "Created": "2026-07-11T15:56:32Z",
      "Owner": "aaaaaaaa-bbbb-cccc-dddd-000000000015"
    },
    {
      "Name": "Sample resource 2",
      "Type": "microsoft.powerautomate/cloudflows",
      "Created": "2026-07-11T15:56:03Z",
      "Owner": "aaaaaaaa-bbbb-cccc-dddd-000000000015"
    },
    {
      "Name": "Sample resource 2",
      "Type": "microsoft.powerautomate/agentflows",
      "Created": "2026-07-11T15:56:01Z",
      "Owner": "aaaaaaaa-bbbb-cccc-dddd-000000000015"
    },
    {
      "Name": "Sample resource 3",
      "Type": "microsoft.powerautomate/cloudflows",
      "Created": "2026-07-11T09:27:16Z",
      "Owner": "aaaaaaaa-bbbb-cccc-dddd-000000000015"
    },
    {
      "Name": "Sample resource 3",
      "Type": "microsoft.powerautomate/agentflows",
      "Created": "2026-07-11T09:27:15Z",
      "Owner": "aaaaaaaa-bbbb-cccc-dddd-000000000015"
    },
    {
      "Name": "Sample resource 4",
      "Type": "microsoft.powerautomate/cloudflows",
      "Created": "2026-07-11T09:26:52Z",
      "Owner": "aaaaaaaa-bbbb-cccc-dddd-000000000015"
    },
    {
      "Name": "Sample resource 4",
      "Type": "microsoft.powerautomate/agentflows",
      "Created": "2026-07-11T09:26:50Z",
      "Owner": "aaaaaaaa-bbbb-cccc-dddd-000000000015"
    },
    {
      "Name": "Sample resource 5",
      "Type": "microsoft.powerautomate/cloudflows",
      "Created": "2026-07-11T09:24:23Z",
      "Owner": "aaaaaaaa-bbbb-cccc-dddd-000000000015"
    },
    {
      "Name": "Sample resource 5",
      "Type": "microsoft.powerautomate/agentflows",
      "Created": "2026-07-11T09:24:22Z",
      "Owner": "aaaaaaaa-bbbb-cccc-dddd-000000000015"
    },
    {
      "Name": "Sample resource 6",
      "Type": "microsoft.powerautomate/cloudflows",
      "Created": "2026-07-11T09:13:55Z",
      "Owner": "aaaaaaaa-bbbb-cccc-dddd-000000000015"
    },
    {
      "Name": "Sample resource 6",
      "Type": "microsoft.powerautomate/agentflows",
      "Created": "2026-07-11T09:13:54Z",
      "Owner": "aaaaaaaa-bbbb-cccc-dddd-000000000015"
    }
  ],
  "facets": []
}
```

[`output.json` (raw)](https://raw.githubusercontent.com/Laskewitz/pac-admin-query/main/samples/new-resources/output.json)
