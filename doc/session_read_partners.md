Read Partners Using Session
=============================

This endpoint allows to retrieve partners data.

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

Postman Demo: [session_read_partners.json](postman_collection.json)

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

| Name        | Type    | Description                                                                   |
|-------------|---------|-------------------------------------------------------------------------------|
| `model`     | string  | The name of the Odoo model on which the method will be executed.              |
| `method`    | string  | The name of the public method to be called on the specified model.            |
| `args`      | list    | The positional arguments of the method, provided as a JSON-encoded list.      |
| `kwargs`    | dict    | The keyword arguments of the method, provided as a JSON-encoded object.       |

Get specific partners in your database
=============================

1. Use `get_partners` method on a database to read specific partners and their fields using domains:

    ```python
    @api.model
    def get_partners(self, offset=0, limit=None):
        """Get Partners."""
        return self._get_partners(offset, limit)
    ```

```
POST /web/dataset/call_kw/res.partner/get_partners
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
|                      | list    | Partner list                                                              |

## Request Example

```json
{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "res.partner",
        "method": "get_partners",
        "args": [],
        "kwargs": { // kwargs
            "offset": 0,
            "limit": 2
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
            "id": 47,
            "name": "Banco de Ahorro y Crédito Confisa, S. A.",
            "debtor_code": "111",
            "vat": "1-01-09009-1"
        },
        {
            "id": 48,
            "name": "Corporación de Crédito Leasing Confisa, S. A.",
            "debtor_code": false,
            "vat": "1-01-15065-3"
        }
    ]
}
```

## cURL Example

```bash
curl --location 'http://localhost:8069/web/dataset/call_kw/res.partner/get_partners' \
--header 'Content-Type: application/json' \
--header 'Cookie: frontend_lang=en_US; session_id=8ace287e798f31740242c2a1cdbe8b45352d7e72' \
--data-raw '{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "res.partner",
        "method": "get_partners",
        "args": [],
        "kwargs": { 
            "offset": 0,
            "limit": 2
        }
    }
}'
```
