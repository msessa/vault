---
layout: "api"
page_title: "MongoDB Secret Backend - HTTP API"
sidebar_current: "docs-http-secret-mongodb"
description: |-
  This is the API documentation for the Vault MongoDB secret backend.
---

# MongoDB Secret Backend HTTP API

This is the API documentation for the Vault MongoDB secret backend. For general
information about the usage and operation of the MongoDB backend, please see
the [Vault MongoDB backend documentation](/docs/secrets/mongodb/index.html).

This documentation assumes the MongoDB backend is mounted at the `/mongodb`
path in Vault. Since it is possible to mount secret backends at any location,
please update your API calls accordingly.

## Configure Connection

This endpoint configures the standard connection string (URI) used to
communicate with MongoDB.

| Method   | Path                         | Produces               |
| :------- | :--------------------------- | :--------------------- |
| `POST`   | `/mongodb/config/connection` | `200 application/json` |

### Parameters

- `url` `(string: <required>)` – Specifies the MongoDB standard connection
  string (URI).

- `verify_connection` `(bool: true)` – Specifies if the connection is verified
  during initial configuration.

### Sample Payload

```json
{
  "url": "mongodb://db1.example.net,db2.example.net:2500/?replicaSet=test"
}
```

### Sample Request

```
$ curl \
    --header "X-Vault-Token: ..." \
    --request POST \
    --data @payload.json \
    https://vault.rocks/v1/mongodb/config/connection
```

### Sample Response

```json
{
  "lease_id": "",
  "renewable": false,
  "lease_duration": 0,
  "data": null,
  "wrap_info": null,
  "warnings": [
    "Read access to this endpoint should be controlled via ACLs as it will return the connection URI as it is, including passwords, if any."
  ],
  "auth": null
}
```

## Read Connection

This endpoint queries the connection configuration. Access to this endpoint
should be controlled via ACLs as it will return the connection URI as it is,
including passwords, if any.

| Method   | Path                         | Produces               |
| :------- | :--------------------------- | :--------------------- |
| `GET`    | `/mongodb/config/connection` | `200 application/json` |

### Sample Request

```
$ curl \
    --header "X-Vault-Token: ..." \
    https://vault.rocks/v1/mongodb/config/connection
```

### Sample Response

```json
{
  "lease_id": "",
  "renewable": false,
  "lease_duration": 0,
  "data": {
    "uri": "mongodb://admin:Password!@mongodb.acme.com:27017/admin?ssl=true"
  },
  "wrap_info": null,
  "warnings": null,
  "auth": null
}
```

## Configure Lease

This endpoint configures the default lease TTL settings for credentials
generated by the mongodb backend.

| Method   | Path                         | Produces               |
| :------- | :--------------------------- | :--------------------- |
| `POST`   | `/mongodb/config/lease`      | `204 (empty body)`     |

### Parameters

- `lease` `(string: <required>)` – Specifies the lease value provided as a
  string duration with time suffix. "h" (hour) is the largest suffix.

- `lease_max` `(string: <required>)` – Specifies the maximum lease value
  provided as a string duration with time suffix. "h" (hour) is the largest
  suffix.

### Sample Payload

```json
{
  "lease": "12h",
  "lease_max": "24h"
}
```

### Sample Request

```
$ curl \
    --header "X-Vault-Token: ..." \
    --request POST \
    --data @payload.json \
    https://vault.rocks/v1/mongodb/config/lease
```

## Read Lease

This endpoint queries the lease configuration.

| Method   | Path                         | Produces               |
| :------- | :--------------------------- | :--------------------- |
| `GET`    | `/mongodb/config/lease`      | `200 application/json` |

### Sample Request

```
$ curl \
    --header "X-Vault-Token: ..." \
    https://vault.rocks/v1/mongodb/config/lease
```

### Sample Response

```json
{
  "lease_id": "",
  "renewable": false,
  "lease_duration": 0,
  "data": {
    "max_ttl": 60,
    "ttl": 60
  },
  "wrap_info": null,
  "warnings": null,
  "auth": null
}
```

## Create Role

This endpoint creates or updates a role definition.

| Method   | Path                         | Produces               |
| :------- | :--------------------------- | :--------------------- |
| `POST`   | `/mongodb/roles/:name`       | `204 (empty body)`     |

### Parameters

- `db` `(string: <required>)` – Specifies the name of the database users should
  be created in for this role.

- `roles` `(string: "")` – Specifies the MongoDB roles to assign to the users
  generated for this role.

### Sample Payload

```json
{
  "db": "my-db",
  "roles": "[\"readWrite\",{\"db\":\"bar\",\"role\":\"read\"}]"
}
```

### Sample Request

```
$ curl \
    --header "X-Vault-Token: ..." \
    --request POST \
    --data @payload.json \
    https://vault.rocks/v1/mongodb/roles/my-role
```

## Read Role

This endpoint queries the role definition.

| Method   | Path                         | Produces               |
| :------- | :--------------------------- | :--------------------- |
| `GET`    | `/mongodb/roles/:name`       | `200 application/json` |

### Parameters

- `name` `(string: <required>)` – Specifies the name of the role to read. This
  is specified as part of the URL.

### Sample Request

```
$ curl \
    --header "X-Vault-Token: ..." \
    https://vault.rocks/v1/mongodb/roles/my-role
```

### Sample Response

```json
{
  "lease_id": "",
  "renewable": false,
  "lease_duration": 0,
  "data": {
    "db": "foo",
    "roles": "[\"readWrite\",{\"db\":\"bar\",\"role\":\"read\"}]"
  },
  "wrap_info": null,
  "warnings": null,
  "auth": null
}
```

## List Roles

This endpoint returns a list of available roles. Only the role names are
returned, not any values.

| Method   | Path                         | Produces               |
| :------- | :--------------------------- | :--------------------- |
| `LIST`   | `/mongodb/roles`             | `200 application/json` |

### Sample Request

```
$ curl \
    --header "X-Vault-Token: ..." \
    --request LIST \
    https://vault.rocks/v1/mongodb/roles
```

### Sample Response

```json
{
  "lease_id": "",
  "renewable": false,
  "lease_duration": 0,
  "data": {
    "keys": [
      "dev",
      "prod"
    ]
  },
  "wrap_info": null,
  "warnings": null,
  "auth": null
}
```

## Delete Role

This endpoint deletes the role definition.

| Method   | Path                         | Produces               |
| :------- | :--------------------------- | :--------------------- |
| `DELETE` | `/mongodb/roles/:name`       | `204 (empty body)`     |

### Parameters

- `name` `(string: <required>)` – Specifies the name of the role to delete. This
  is specified as part of the URL.

### Sample Request

```
$ curl \
    --header "X-Vault-Token: ..." \
    --request DELETE \
    https://vault.rocks/v1/mongodb/roles/my-role
```

## Generate Credentials

This endpoint generates a new set of dynamic credentials based on the named
role.

| Method   | Path                         | Produces               |
| :------- | :--------------------------- | :--------------------- |
| `GET`    | `/mongodb/creds/:name`       | `200 application/json` |

### Parameters

- `name` `(string: <required>)` – Specifies the name of the role to create
  credentials against. This is specified as part of the URL.

### Sample Request

```
$ curl \
    --header "X-Vault-Token: ..." \
    https://vault.rocks/v1/mongodb/creds/my-role
```

### Sample Response

```json
{
  "lease_id": "mongodb/creds/readonly/e64e79d8-9f56-e379-a7c5-373f9b4ee3d8",
  "renewable": true,
  "lease_duration": 3600,
  "data": {
    "db": "foo",
    "password": "de0f7b50-d700-54e5-4e81-5c3724283999",
    "username": "vault-token-b32098cb-7ff2-dcf5-83cd-d5887cedf81b"
  },
  "wrap_info": null,
  "warnings": null,
  "auth": null
}
```
