---
title: Addons
layout: default
---

# Addons

--- row ---

**Addon attributes**

{:.table}
| field            | type   | description                                   |
| ---------------- | ------ | --------------------------------------------- |
| id               | string | unique ID identifying the addon               |
| provisioned_at   | date   | when the addon has been created               |
| deprovisioned_at | date   | when the addon has been removed/upgraded      |
| resource_id      | string | resource reference                            |
| plan             | object | embedded reference to Plan resource           |
| addon_provider   | object | embedded reference to AddonProvider resource  |

||| col |||

Example object:

```json
{
  "id" : "5415beca646173000b015000",
  "provisioned_at": "2015-02-22T18:55:02.766+01:00",
  "deprovisioned_at": null,
  "plan" : {
    "description" : "[Markdown description]",
    "display_name" : "64MB Free tier",
    "id" : "52fd2357356330032b080000",
    "name" : "free",
    "price" : 0.0
  },
  "resource_id" : "example_app_3083",
  "addon_provider" : {
    "id" : "scalingo-redis",
    "name" : "Scalingo Redis",
    "logo_url" : "//storage.sbg1.cloud.ovh.net/v1/AUTH_be65d32d71a6435589a419eac98613f2/scalingo/redis.png"
  }
}
```

--- row ---

## List application addons

--- row ---

`GET https://$SCALINGO_API_URL/v1/apps/[:app]/addons`

List all the provisioned addons for a given application.

||| col |||

Example

```shell
curl -H "Accept: application/json" -H "Content-Type: application/json" \
  -H "Authorization: Bearer $BEARER_TOKEN" \
  -X GET https://$SCALINGO_API_URL/v1/apps/example-app/addons
```

Returns 200 OK

```json
{
    "addons": [{
	 "id" : "5415beca646173000b015000",
         "provisioned_at": "2015-02-22T18:55:02.766+01:00",
         "deprovisioned_at": null,
         "plan" : {
            "description" : "[Markdown description]",
            "display_name" : "64MB Free tier",
            "id" : "52fd2357356330032b080000",
            "name" : "free",
            "price" : 0
         },
         "resource_id" : "example_app_3083",
         "addon_provider" : {
            "id" : "scalingo-redis",
            "name" : "Scalingo Redis",
            "logo_url" : "//storage.sbg1.cloud.ovh.net/v1/AUTH_be65d32d71a6435589a419eac98613f2/scalingo/redis.png"
         }
    }]
}
```

--- row ---

## Provision an addon

--- row ---

`POST https://$SCALINGO_API_URL/v1/apps/[:app]/addons`

Provision the addon defined by the `addon_provider` at the given plan.
The resource should be instantly available, it modifies environment
variables.

### Parameters

* `addon.addon_provider_id`
* `addon.plan_id`

Free usage limit:

You can only use free addons without having defined [a payment
method](https://my.scalingo.com/apps/billing).

||| col |||

```shell
curl -H "Accept: application/json" -H "Content-Type: application/json" \
  -H "Authorization: Bearer $BEARER_TOKEN" \
  -X POST https://$SCALINGO_API_URL/v1/apps/[:app]/addons \
  -d '{"addon":{"plan_id": "1234", "addon_provider_id": "1234"}}'
```

--- row ---

## Upgrade an addon

--- row ---

`PATCH https://$SCALINGO_API_URL/v1/apps/[:app]/addons/[:addon_id]`

Change your addon plan. The endpoint will query the addon provider to
upgrade your plan to a new one.

Be cautious though, the operation might fail. The provider may refuse to
downgrade an addon if the operation is invalid. Example: You have a 3GB
database and you want to return to the free tier database at 512MB.

### Parameters

* `addon.plan_id`

Free usage limit:

You can only use free addons without having defined [a payment
method](https://my.scalingo.com/apps/billing).


||| col |||

```shell
curl -H "Accept: application/json" -H "Content-Type: application/json" \
  -H "Authorization: Bearer $BEARER_TOKEN" \
  -X PATCH https://$SCALINGO_API_URL/v1/apps/[:app]/addons/[:addon_id] \
  -d '{"addon": {"plan_id": "2"}}'
```

Returns 200 OK

```json
{
  "vars": ["VAR1", "VAR2"],
  "message": "<custom message from the addon provider>"
}
```

--- row ---

## Remove an addon

--- row ---

`DELETE https://$SCALINGO_API_URL/v1/apps/[:app]/addons/[:addon_id]`

Request deprovisionning of the addon. This may be a dangerous operation as the
addon provider will certainly erase all the data related to your application.
Be cautious when deleting addons, be sure of what you're doing.

||| col |||

```shell
curl -H "Accept: application/json" -H "Content-Type: application/json" \
  -H "Authorization: Bearer $BEARER_TOKEN" \
  -X DELETE https://$SCALINGO_API_URL/v1/apps/[:app]/addons/[:addon_id] \
```

Returns 204 No Content

--- row ---

## Get addon token

--- row ---

`POST https://$SCALINGO_API_URL/v1/apps/[:app]/addons/[:addon_id]/token`

Request a token usable against the addon API. This token will only be valid for
an hour.


||| col |||

```json
{
  "addon": {
    "id": "5c599fd66409d300019abf32",
    "app_id": "5b0bfa3b3a6bbc00017e4e3d",
    "resource_id": "lolapp-2107",
    "addon_provider": {
      "id": "scalingo-redis",
      "name": "Scalingo Redis",
      "logo_url": "//cdn.scalingo.com/addons/Scalingo_Redis.svg"
    },
    "plan": {
      "id": "599c1a2121276700011caadc",
      "name": "free",
      "display_name": "64MB Free tier",
      "price": 0,
      "description": "<p>Free plan</p>\n",
      "text_description": null,
      "position": 1,
      "on_demand": null
    },
    "provisioned_at": "2019-02-05T15:38:14.223+01:00",
    "deprovisioned_at": null,
    "status": "provisioning",
    "token": "[REDACTED]"
  }
}
```

