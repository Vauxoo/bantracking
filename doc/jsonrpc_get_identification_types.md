Get the identification types using Jsonrpc
=============================

This controller retrieves the identification types for owners that will be sent in the orders. The request should only be made to the endpoint without sending any parameters.

Call a method
---

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

Postman Demo: [jsonrpc_get_identification_types.json](postman_collection.json)

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
| `[2] - password`       | string  | Password or [Token](https://www.odoo.com/documentation/16.0/developer/reference/external_api.html#api-keys)|
| `[3] - model`          | string  | The name of the Odoo model on which the method will be executed.        |
| `[4] - method`         | string  | The name of the public method to be called on the specified Odoo model. |
| `[5] - args`           | list    | The positional arguments of the method, provided as a JSON-encoded list.|
| `[6] - kwargs`         | dict    | The keyword arguments of the method, provided as a JSON-encoded object. |

Read Types of Identification in your database
=============================

1. Use `get_identification_types` method on a database to retrieve the identification types:

    ```python
    @api.model
    def get_identification_types(self):
        """Get a list of identification types.
        Returns:
            list: A list of dictionaries containing an id an the type of identification 
        """
        identification_types = [
            {
                "id": idx + 1,
                "Identificacion": name.upper().replace("PASAPORTE", "PASSPORT"),
            }
            for idx, (key, name) in enumerate(IDENTIFICATION_TYPES)
        ]
        return identification_types
    ```

## Input Parameters

No input values are required into the args list.

## Response

The method returns a JSON object as a response:

| Name                 | Type    | Description                                                               |
|----------------------|---------|---------------------------------------------------------------------------|
|                      | list    | Identification types list of dictionaries                                 |

## Request Example

```json
{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "service": "object",
        "method": "execute",
        "args": [
            "confisa", // database
            2, // uid
            "admin", // password or token
            "sale.order", // model
            "get_identification_types" // public method
        ]
    }
}
```

## Response Example

```json
{
    "jsonrpc": "2.0",
    "id": 16,
    "result": [
        {
            "id": 1,
            "Identificacion": "CEDULA"
        },
        {
            "id": 2,
            "Identificacion": "PASSPORT"
        },
        {
            "id": 3,
            "Identificacion": "RNC"
        }
    ]
}
```

## cURL Example

```bash
curl --location --request GET 'http://localhost:8069/jsonrpc' \
--header 'Content-Type: application/json' \
--data-raw '{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "service": "object",
        "method": "execute",
        "args": [
            "confisa", 
            2, 
            "admin", 
            "sale.order",
            "get_identification_types"
        ]
    }
}'
```
