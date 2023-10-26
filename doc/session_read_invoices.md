Read Invoices Using Session
=============================

This endpoint allows to retrieve all the invoices for example in draft state, but it is recommended to set a limit.

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

Postman Demo: [session_read_invoices.json](postman_collection.json)

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

| Name        | Type    | Description                                                                   |
|-------------|---------|-------------------------------------------------------------------------------|
| `model`     | string  | The name of the Odoo model on which the method will be executed.              |
| `method`    | string  | The name of the public method to be called on the specified model.            |
| `args`      | list    | The positional arguments of the method, provided as a JSON-encoded list.      |
| `kwargs`    | dict    | The keyword arguments of the method, provided as a JSON-encoded object.       |

Read Invoices in your database
=============================

1. Use `get_invoices` method on a database to read invoices and their fields using domains:

    ```python
    @api.model
    def get_invoices(self, offset=0, limit=None):
        """Get invoices."""
        return self._get_invoices(offset, limit)
    ```

## URL

```
POST /web/dataset/call_kw/account.move/get_invoices
```

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
|                      | list    | Invoices list                                                              |

## Request Example

```json
{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "account.move",
        "method": "get_invoices",
        "args": [],
        "kwargs": {
            "offset": 0,
            "limit": 3
        }
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
            "id": 45,
            "name": "/",
            "state": "draft"
        },
        {
            "id": 46,
            "name": "/",
            "state": "draft"
        }
    ]
}
```

## cURL Example

```bash
curl --location 'http://localhost:8069/web/dataset/call_kw/account.move/get_invoices' \
--header 'Content-Type: application/json' \
--header 'Cookie: frontend_lang=en_US; session_id=8ace287e798f31740242c2a1cdbe8b45352d7e72' \
--data '{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "account.move",
        "method": "get_invoices",
        "args": [],
        "kwargs": {
            "fields": [ 
                "id",
                "name",
                "state"
            ],
            "offset": 0,
            "limit": 3
        }
    }
}'
```
