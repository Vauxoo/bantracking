Pinging the service to check its availability
===========================================

Call a method
-------------

1. Use `jsonrpc` to execute specific Odoo methods with JSON-RPC and receive the results back in JSON format:

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

Postman Demo: [jsonrpc_ping.json](postman_collection.json)

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

Ping the service to check its availability and whether it's receiving requests
-------------------------------------

1. Use `ping` method on a database to validate if the service is active in Odoo:

    ```python
    @api.model
    def ping(self):
        """Check the status of the server.
        Returns:
            Response: A true boolean value indicating the server is up.
        """
        return Response("true", status=200, headers={"Content-Type": "application/json"})
    ```

## Input Parameters

No input values are used into the args list.

## Response

The method returns a BOOL as a response:

| Name                  | Type    | Description                                   |
|-----------------------|---------|-----------------------------------------------|
|                       | boolean | A true boolean if the server is up.           |

## Request Example

```json
{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "service": "object",
        "method": "execute", // using `execute` instead of `execute_kw` allows to avoid the empty arg and kwargs
        "args": [
            "db_name", // database name
            2,  // uid
            "admin", // password or token
            "res.users",
            "ping"
        ]
    }
}
```

## Response Example

```json
{
    "jsonrpc": "2.0",
    "id": 16,
    "result": true
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
        "service": "object",
        "method": "execute",
        "args": [
            "db_name",
            2,
            "admin",
            "res.users",
            "ping"
        ]
    }
}'
```
