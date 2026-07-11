# 🗂️ Environment inventory

List every environment with its region, type, managed state, and group in one flat table. Exported as CSV so you can drop it straight into a spreadsheet.

## Query

```json
{
  "TableName": "PowerPlatformResources",
  "Clauses": [
    {
      "$type": "where",
      "FieldName": "type",
      "Operator": "==",
      "Values": [
        "'microsoft.powerplatform/environments'"
      ]
    },
    {
      "$type": "project",
      "FieldList": [
        "EnvironmentId = name",
        "Name = properties.displayName",
        "Region = location",
        "EnvironmentType = properties.environmentType",
        "IsManaged = properties.isManaged",
        "EnvironmentGroup = properties.environmentGroup",
        "EnvironmentGroupId = properties.environmentGroupId",
        "LastModifiedAt = properties.lastModifiedAt"
      ]
    },
    {
      "$type": "orderby",
      "FieldNamesAscDesc": {
        "tostring(Name)": "asc"
      }
    }
  ],
  "Options": {
    "Top": 500,
    "Skip": 0,
    "SkipToken": ""
  }
}
```

[download](https://raw.githubusercontent.com/Laskewitz/pac-admin-query/main/samples/environments/query.json)

## Run it

```bash
pac admin query --query-file samples/environments/query.json --output-type Grid
```

## Sample output

Sanitized excerpt from a live tenant.

```csv
EnvironmentId,Name,Region,EnvironmentType,IsManaged,EnvironmentGroup,EnvironmentGroupId,LastModifiedAt
aaaaaaaa-bbbb-cccc-dddd-000000000007,Contoso Environment 1,unitedstates,Sandbox,False,,,2026-06-08T23:07:55.8099709Z
aaaaaaaa-bbbb-cccc-dddd-000000000008,Contoso Environment 2,unitedstates,Developer,True,,aaaaaaaa-bbbb-cccc-dddd-000000000009,2026-07-07T16:44:22.709023Z
aaaaaaaa-bbbb-cccc-dddd-000000000010,Contoso Environment 3,unitedstates,Sandbox,False,,,2026-06-08T15:04:29.9019404Z
aaaaaaaa-bbbb-cccc-dddd-000000000011,Contoso Environment 4,unitedstates,Developer,True,,aaaaaaaa-bbbb-cccc-dddd-000000000009,2026-07-07T16:44:13.8049446Z
aaaaaaaa-bbbb-cccc-dddd-000000000012,Contoso Environment 5,unitedstates,Developer,True,,aaaaaaaa-bbbb-cccc-dddd-000000000009,2026-07-07T16:44:14.2565807Z
aaaaaaaa-bbbb-cccc-dddd-000000000013,Contoso Environment 6,unitedstates,Sandbox,True,,aaaaaaaa-bbbb-cccc-dddd-000000000009,2026-07-07T16:44:20.8619819Z
aaaaaaaa-bbbb-cccc-dddd-000000000014,Contoso Environment 7,unitedstates,Sandbox,False,,,2026-06-09T17:35:38.6089548Z
aaaaaaaa-bbbb-cccc-dddd-000000000015,Contoso Environment 8,unitedstates,Developer,True,,aaaaaaaa-bbbb-cccc-dddd-000000000009,2026-07-07T16:44:13.8356448Z
Default-aaaaaaaa-bbbb-cccc-dddd-000000000016,Contoso Environment 9,unitedstates,Default,False,,,2026-06-08T09:27:04.3420718Z
aaaaaaaa-bbbb-cccc-dddd-000000000017,Contoso Environment 10,unitedstates,Developer,True,,aaaaaaaa-bbbb-cccc-dddd-000000000009,2026-07-07T16:44:20.4786317Z
aaaaaaaa-bbbb-cccc-dddd-000000000018,Contoso Environment 11,unitedstates,Sandbox,False,,,2026-07-07T18:53:32.141399Z
aaaaaaaa-bbbb-cccc-dddd-000000000019,Contoso Environment 12,unitedstates,Sandbox,False,,,2026-06-09T17:36:10.4717396Z
aaaaaaaa-bbbb-cccc-dddd-000000000020,Contoso Environment 13,unitedstates,Production,True,,,2026-06-17T11:28:26.0668382Z
aaaaaaaa-bbbb-cccc-dddd-000000000021,Contoso Environment 14,unitedstates,Sandbox,True,,aaaaaaaa-bbbb-cccc-dddd-000000000009,2026-07-07T16:44:19.6396403Z
aaaaaaaa-bbbb-cccc-dddd-000000000022,Contoso Environment 15,unitedstates,Sandbox,True,,aaaaaaaa-bbbb-cccc-dddd-000000000009,2026-07-07T16:45:11.2374719Z
aaaaaaaa-bbbb-cccc-dddd-000000000023,Contoso Environment 16,unitedstates,Sandbox,True,,aaaaaaaa-bbbb-cccc-dddd-000000000009,2026-07-07T16:44:14.2712274Z
aaaaaaaa-bbbb-cccc-dddd-000000000024,Contoso Environment 17,unitedstates,Sandbox,False,,,2026-06-23T23:18:43.0977164Z
aaaaaaaa-bbbb-cccc-dddd-000000000025,Contoso Environment 18,unitedstates,Sandbox,False,,,2026-06-08T15:02:37.7718538Z
aaaaaaaa-bbbb-cccc-dddd-000000000026,Contoso Environment 19,unitedstates,Sandbox,False,,,2026-06-28T23:20:02.7475268Z
```

[download](https://raw.githubusercontent.com/Laskewitz/pac-admin-query/main/samples/environments/output.csv)
