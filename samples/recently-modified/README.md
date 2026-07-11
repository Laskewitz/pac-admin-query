# ✏️ Recently modified

Watch for churn. This surfaces everything touched in the past week, newest first, which is handy for catching active development or changes you didn't expect.

> [!NOTE]
> Output is truncated to 20 of 379 rows. `totalRecords` shows the real count.

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
      "FieldName": "Modified",
      "Expression": "todatetime(properties.lastModifiedAt)"
    },
    {
      "$type": "where",
      "FieldName": "Modified",
      "Operator": ">=",
      "Values": [
        "ago(7d)"
      ]
    },
    {
      "$type": "project",
      "FieldList": [
        "Name = properties.displayName",
        "Type = type",
        "Modified"
      ]
    },
    {
      "$type": "orderby",
      "FieldNamesAscDesc": {
        "Modified": "desc"
      }
    }
  ]
}
```

[Download](https://raw.githubusercontent.com/Laskewitz/pac-admin-query/main/samples/recently-modified/query.json)

## Run it

```bash
pac admin query --query-file samples/recently-modified/query.json --output-type Json
```

## Sample output

Sanitized excerpt from a live tenant.

```json
{
  "totalRecords": 379,
  "count": 20,
  "resultTruncated": 1,
  "skipToken": null,
  "data": [
    {
      "Name": "Sample resource 1",
      "Type": "microsoft.powerautomate/agentflows",
      "Modified": "2026-07-11T16:07:52.489653Z"
    },
    {
      "Name": "Sample resource 1",
      "Type": "microsoft.powerautomate/cloudflows",
      "Modified": "2026-07-11T16:07:40.6012591Z"
    },
    {
      "Name": "Sample resource 1",
      "Type": "microsoft.powerautomate/agentflows",
      "Modified": "2026-07-11T16:07:29.6790511Z"
    },
    {
      "Name": "Sample resource 1",
      "Type": "microsoft.powerautomate/cloudflows",
      "Modified": "2026-07-11T16:07:18.1357067Z"
    },
    {
      "Name": "Sample resource 1",
      "Type": "microsoft.powerautomate/agentflows",
      "Modified": "2026-07-11T16:07:06.8797109Z"
    },
    {
      "Name": "Sample resource 1",
      "Type": "microsoft.powerautomate/cloudflows",
      "Modified": "2026-07-11T16:06:54.3306961Z"
    },
    {
      "Name": "Sample resource 2",
      "Type": "microsoft.powerautomate/agentflows",
      "Modified": "2026-07-11T15:57:11.8839124Z"
    },
    {
      "Name": "Sample resource 2",
      "Type": "microsoft.powerautomate/cloudflows",
      "Modified": "2026-07-11T15:56:58.2019219Z"
    },
    {
      "Name": "Sample resource 2",
      "Type": "microsoft.powerautomate/agentflows",
      "Modified": "2026-07-11T15:56:46.7355756Z"
    },
    {
      "Name": "Sample resource 2",
      "Type": "microsoft.powerautomate/cloudflows",
      "Modified": "2026-07-11T15:56:33.9885417Z"
    },
    {
      "Name": "Sample resource 2",
      "Type": "microsoft.powerautomate/agentflows",
      "Modified": "2026-07-11T15:56:22.9243912Z"
    },
    {
      "Name": "Sample resource 2",
      "Type": "microsoft.powerautomate/cloudflows",
      "Modified": "2026-07-11T15:56:03.2734382Z"
    },
    {
      "Name": "Sample resource 3",
      "Type": "microsoft.powerautomate/agentflows",
      "Modified": "2026-07-11T09:27:28.6668269Z"
    },
    {
      "Name": "Sample resource 3",
      "Type": "microsoft.powerautomate/cloudflows",
      "Modified": "2026-07-11T09:27:16.9286373Z"
    },
    {
      "Name": "Sample resource 4",
      "Type": "microsoft.powerautomate/agentflows",
      "Modified": "2026-07-11T09:27:05.1363744Z"
    },
    {
      "Name": "Sample resource 4",
      "Type": "microsoft.powerautomate/cloudflows",
      "Modified": "2026-07-11T09:26:52.0297358Z"
    },
    {
      "Name": "Sample resource 5",
      "Type": "microsoft.powerautomate/agentflows",
      "Modified": "2026-07-11T09:24:36.2040219Z"
    },
    {
      "Name": "Sample resource 5",
      "Type": "microsoft.powerautomate/cloudflows",
      "Modified": "2026-07-11T09:24:23.4661459Z"
    },
    {
      "Name": "Sample resource 6",
      "Type": "microsoft.powerautomate/agentflows",
      "Modified": "2026-07-11T09:14:08.4306882Z"
    },
    {
      "Name": "Sample resource 6",
      "Type": "microsoft.powerautomate/cloudflows",
      "Modified": "2026-07-11T09:13:55.458868Z"
    }
  ],
  "facets": []
}
```

[Download](https://raw.githubusercontent.com/Laskewitz/pac-admin-query/main/samples/recently-modified/output.json)
