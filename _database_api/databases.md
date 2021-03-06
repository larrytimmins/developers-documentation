---
title: Databases
layout: default
---

# Databases

--- row ---

**Databases attributes**

{:.table}
| field                         | type    | description                                    |
| ------------------            | ------- | ---------------------------------------------- |
| id                            | string  | unique ID                                      |
| resource_id                   | string  | resource reference                             |
| app_name                      | string  | name of the linked application                 |
| created_at                    | date    | creation date of the database                  |
| encryption_at_rest            | boolean | is encryption at rest enabled on this database |
| plan                          | string  | name of the application plan                   |
| status                        | string  | status of the current database                 |
| type_id                       | string  | database type ID                               |
| type_name                     | string  | database type Name                             |
| version_id                    | string  | database version ID                            |
| readable_version              | string  | human readable database version                |
| instances                     | array   | list of all database instances                 |
| features                      | array   | list of all database features                  |
| periodic_backups_enabled      | boolean | true if periodic backups are enabled           |
| periodic_backups_scheduled_at | array   | hours of the day of the periodic backup (UTC)  |

**Instance attributes**

{:.table}
| field    | type    | description                               |
| -------- | ------  | ----------------------------------------- |
| id       | string  | unique ID                                 |
| hostname | string  | FQDN of the instance                      |
| port     | integer | instance port                             |
| status   | string  | status of the current instance            |
| type     | string  | is this node a database node or a gateway |

||| col |||

Example object:

```json
{
  "database": {
    "id": "5c599fd6f18b3202f7ab4e66",
    "resource_id": "lolapp-2107",
    "app_name": "lolapp",
    "created_at": "2019-02-05T15:38:14.343+01:00",
    "encryption_at_rest": true,
    "features": [
      {
        "name": "redis-rdb",
        "status": "ACTIVATED"
      }
    ],
    "plan": "free",
    "status": "running",
    "type_id": "5bf30d1104c87f000161285a",
    "type_name": "redis",
    "version_id": "5bf30d1104c87f000161285b",
    "instances": [
      {
        "id": "a7ecbaf9-7f3c-4324-bf35-975f546718c2",
        "hostname": "a7ecbaf9-7f3c-4324-bf35-975f546718c2.test-db.redis.dbs.scalingo.com",
        "port": 1234,
        "status": "running",
        "type": "db-node",
      }
    ],
    "readable_version": "3.2.9-1",
    "periodic_backups_enabled": true,
    "periodic_backups_scheduled_at": [0]
  }
}
```

--- row ---

## Retrieve Database Information

--- row ---

`GET https://db-api.scalingo.com/api/databases/[:db_id]`

Retrieve information of a specific database.

To find the database ID you must use our [addon API documentation](/addons).
The ID to use correspond to the addon's `id` field.


||| col |||

Example request

```shell
curl -H "Accept: application/json" -H "Content-Type: application/json" \
  -H "Authorization: Bearer $DB_BEARER_TOKEN" \
  -X GET https://db-api.scalingo.com/api/databases/my-db-123
```

Returns 200 OK

```json
{
  "database": {
    "id": "5c599fd6f18b3202f7ab4e66",
    "resource_id": "lolapp-2107",
    "app_name": "lolapp",
    "created_at": "2019-02-05T15:38:14.343+01:00",
    "encryption_at_rest": true,
    "features": [
      {
        "name": "redis-rdb",
        "status": "ACTIVATED"
      }
    ],
    "plan": "free",
    "status": "running",
    "type_id": "5bf30d1104c87f000161285a",
    "type_name": "redis",
    "version_id": "5bf30d1104c87f000161285b",
    "instances": [],
    "readable_version": "3.2.9-1",
    "periodic_backups_enabled": true,
    "periodic_backups_scheduled_at": [0]
  }
}
```

--- row ---

## Update a Database

--- row ---

`PATCH https://db-api.scalingo.com/api/databases/[:db_id]`

or

`PUT https://db-api.scalingo.com/api/databases/[:db_id]`

Update database settings.

||| col |||

Example request

```shell
curl -H "Accept: application/json" -H "Content-Type: application/json" \
  -H "Authorization: Bearer $DB_BEARER_TOKEN" \
  -X PATCH https://db-api.scalingo.com/api/databases/my-db-123 \
  -d '{
    "database": {
      "periodic_backups_enabled": true,
      "periodic_backups_scheduled_at": 3
    }
  }'
```

Returns 200 OK

```json
{
  "database": {
    "id": "5c599fd6f18b3202f7ab4e66",
    "resource_id": "lolapp-2107",
    "app_name": "lolapp",
    "created_at": "2019-02-05T15:38:14.343+01:00",
    "encryption_at_rest": true,
    "features": [
      {
        "name": "redis-rdb",
        "status": "ACTIVATED"
      }
    ],
    "plan": "free",
    "status": "running",
    "type_id": "5bf30d1104c87f000161285a",
    "type_name": "redis",
    "version_id": "5bf30d1104c87f000161285b",
    "instances": [],
    "readable_version": "3.2.9-1",
    "periodic_backups_enabled": true,
    "periodic_backups_scheduled_at": [3]
  }
}
```

--- row ---

## Point-in-Time Restoration

--- row ---

`POST https://db-api.scalingo.com/api/databases/[:db_id]/pitr/restore`

Restore the database data at a specific point in time. This is currently only
available for PostgreSQL databases.

||| col |||

Example request

```shell
curl -H "Accept: application/json" -H "Content-Type: application/json" \
  -H "Authorization: Bearer $DB_BEARER_TOKEN" \
  -X POST https://db-api.scalingo.com/api/databases/my-db-123/pitr/restore \
  -d '{
    "restore_time": "2019-07-18T23:00:00Z"
  }'
```

Returns 201 Created

```json
{
  "operation_id": "5c10e85ca506b701f42f92dc"
}
```
