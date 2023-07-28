Create Token for authentication
===========================================

Call a method
-------------

1. You can use `jsonrpc` to execute specific Odoo methods with JSON-RPC and receive the results back in JSON format:

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

Postman Demo: [jsonrpc_create_token.json](postman_collection.json)

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

| Name        | Type    | Description                                                                        |
|-------------|---------|------------------------------------------------------------------------------------|
| `service`   | string  | Type of service. (common, db, object)                                              |
| `method`    | string  | Name of the Odoo method to call.                                                   |
| `args`      | list    | The positional arguments of the method, provided as a JSON-encoded list.           |

## `args` structure

| Name                   | Type    | Description                                                                                                  |
|------------------------|---------|-------------------------------------------------------------------------|
| `[0] - db_name`        | string  | Odoo database name                                                      |
| `[1] - uid`            | integer | User ID                                                                 |
| `[2] - password`       | string  | Password                                                                |
| `[3] - model`          | string  | The name of the Odoo model on which the method will be executed.        |
| `[4] - method`         | string  | The name of the public method to be called on the specified Odoo model. |
| `[5] - args`           | list    | The positional arguments of the method, provided as a JSON-encoded list.|
| `[6] - kwargs`         | dict    | The keyword arguments of the method, provided as a JSON-encoded object. |

Generate a valid Token in your database
-------------------------------------

1. Use `get_token` method on a database to get a token with authenticated user in Odoo:

    ```python
    @api.model
    def get_token(self):
        """Method to retrieve the generated token.
        """
        return self._get_token()
    ```

## Input Parameters

No input values are required into the args list.

## Response

The method returns a STR as a response:

| Name                  | Type    | Description                                   |
|-----------------------|---------|-----------------------------------------------|
|                       | string  | Token for the authenticated user in Odoo.     |

## Request Example

```json
{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "service": "object",
        "method": "execute_kw",
        "args": [
            "db_name", // database name
            "login",  // Username
            "admin", // password
            "res.users",
            "get_token",
            [],
            {
                "context": {}
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
    "result": "e46d9a8cfa843f4fe4bfb4e2a3f07d3aff309892" // Token
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
            "res.users",
            "get_token",
            [],
            {
                "context": {}
            }
        ]
    }
}'
```
