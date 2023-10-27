Read Sale temporal Recurrence using Jsonrpc
=============================

This endpoint allows to retrieve the options of recurrenc for a Sale order. 

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

Postman Demo: [jsonrpc_read_recurrence.json](postman_collection.json)

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

Read all sale temporal recurrences in your database
=============================

1. Use `get_sale_recurrence` method on a database to read all or specific recurrences and their fields using domains:

    ```python
    @api.model
    def get_sale_recurrence(self, offset=0, limit=None):
        """Get Sale Recurrence."""
        return self._get_sale_recurrence(offset, limit)
    ```

## URL

```
POST /jsonrpc
```

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

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

## `args` structure

No input values are required into the args list.

## `kwargs` structure

| Name                   | Type    | Description                                                             |
|------------------------|---------|-------------------------------------------------------------------------|
| `offset`               | integer | The offset for the records to retrieve                                  |
| `limit`                | integer | Maximum limit of records to retrieve                                    |

## Response

The method returns a JSON object as a response:

| Name                 | Type    | Description                                                               |
|----------------------|---------|---------------------------------------------------------------------------|
|                      | list    | Recurrence list                                                              |

## Request Example

```json
{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "service": "object",
        "method": "execute_kw",
        "args": [
            "confisa", // database
            2, // uid
            "admin", // password or token
            "sale.temporal.recurrence", // model
            "get_sale_recurrence", // public method
            [],
            { // kwargs
                "offset": 0,
                "limit": 5
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
    "result": [
        {
            "id": 3,
            "name": "Monthly",
            "active": true
        },
        {
            "id": 5,
            "name": "12 meses",
            "active": true
        },
        {
            "id": 6,
            "name": "36 meses",
            "active": true
        },
        {
            "id": 8,
            "name": "24 meses",
            "active": true
        },
        {
            "id": 9,
            "name": "18 meses",
            "active": true
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
        "method": "execute_kw",
        "args": [
            "confisa", 
            2, 
            "admin", 
            "sale.temporal.recurrence",
            "get_sale_recurrence",
            [],
            {
                "offset": 0,
                "limit": 5
            }
        ]
    }
}'
```
