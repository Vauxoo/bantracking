API
===

Login to your database
---

1. Use `authenticate` method to authenticate a user in Odoo with JSON-RPC:

    ```python
    @route('/jsonrpc', type='json', auth="none", save_session=False)
    def jsonrpc(self, service, method, args):
        """ Method used by client APIs to contact Odoo. """
        return dispatch_rpc(service, method, args)
    ```

## URL

```
POST /jsonrpc
```

Postman Demo: [jsonrpc_authenticate.json](postman_collection.json)

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

| Name        | Type    | Description                                             |
|-------------|---------|---------------------------------------------------------|
| `service`   | string  | Type of service. (common, db, object)                   |
| `method`    | string  | Name of the method to call.                             |
| `args`      | list    | ['db_name', 'login', 'password/token']                  |

## Generating api token

(<https://www.odoo.com/documentation/16.0/developer/reference/external_api.html#api-keys>)

## Response

The method returns an INT as a response:

| Name                  | Type    | Description                                   |
|-----------------------|---------|-----------------------------------------------|
|                       | integer | ID of the authenticated user in Odoo.         |

## Request Example

```json
{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "service": "common",
        "method": "authenticate",
        "args": [
            "db_name", // database name
            "login",  // Username
            "01d89daf7ae51840870ea5938491a4a8225590ea", // password or token
            {
                "interactive": false
            }
        ]
    }
}
```

## Response Example

```json
{
    "jsonrpc": "2.0",
    "id": 16,
    "result": 2 // uid
}
```

## cURL Example

```bash
curl --location 'http://localhost:8069/jsonrpc' \
--header 'Content-Type: application/json' \
--data '{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "service": "common",
        "method": "authenticate",
        "args": [
            "db_name",
            "admin",
            "admin",
            {
                "interactive": false
            }
        ]
    }
}'
```
