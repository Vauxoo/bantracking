Get the identification types Using Session
=============================

This controller retrieves the identification types for owners that will be sent in the orders. The request should only be made to the endpoint without sending any parameters.

Call a method
-------------

1. Use `call_kw` to execute specific methods in Odoo server and receive the results back in JSON format:

    ```python
    @http.route(['/web/dataset/call_kw', '/web/dataset/call_kw/<path:path>'], type='json', auth="user")
    def call_kw(self, model, method, args, kwargs, path=None):
        return self._call_kw(model, method, args, kwargs)
    ```

## URL

```
POST /web/dataset/call_kw
```

Postman Demo: [session_get_identification_types.json](postman_collection.json)

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

| Name        | Type    | Description                                                                   |
|-------------|---------|-------------------------------------------------------------------------------|
| `model`     | string  | The name of the Odoo model on which the method will be executed.              |
| `method`    | string  | The name of the public method to be called on the specified model.            |
| `args`      | list    | The positional arguments of the method, provided as a JSON-encoded list.      |
| `kwargs`    | dict    | The keyword arguments of the method, provided as a JSON-encoded object.       |

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

## URL

```
POST /web/dataset/call_kw/sale.order/get_identification_types
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
        "model": "sale.order",
        "method": "get_identification_types",
        "args": [],
        "kwargs": {}
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
curl --location 'http://localhost:8069/web/dataset/call_kw/sale.order/get_identification_types' \
--header 'Content-Type: application/json' \
--header 'Cookie: frontend_lang=en_US; session_id=8ace287e798f31740242c2a1cdbe8b45352d7e72' \
--data '{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "sale.order",
        "method": "get_identification_types",
        "args": [],
        "kwargs": {}
    }
}'
```
