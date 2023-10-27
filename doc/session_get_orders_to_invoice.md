Get Sale Orders to invoice Using Session
===========================================

This endpoint allows you to retrieve all orders that are not in the `draft` or `cancel` state

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

Postman Demo: [session_get_orders_to_invoice.json](postman_collection.json)

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

| Name        | Type    | Description                                                                   |
|-------------|---------|-------------------------------------------------------------------------------|
| `model`     | string  | The name of the Odoo model on which the method will be executed.              |
| `method`    | string  | The name of the public method to be called on the specified model.            |
| `args`      | list    | The positional arguments of the method, provided as a JSON-encoded list.      |
| `kwargs`    | dict    | The keyword arguments of the method, provided as a JSON-encoded object.       |

Get Sale Orders to Invoice
-------------------------------------

1. Use `get_orders_to_invoice` method on a database to get the Sale orders to invoice with authenticated user in Odoo:

    ```python
    @api.model
    def get_orders_to_invoice(self, domain=None, fields=None, offset=0, limit=None, order=None, **read_kwargs):
        """Get orders to invoice."""
        return self._get_orders_to_invoice(domain, fields, offset, limit, order, **read_kwargs)
    ```

## URL

```
POST /web/dataset/call_kw/sale.order/get_orders_to_invoice
```

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

## `args` structure

| Name                   | Type    | Description                                                             |
|------------------------|---------|-------------------------------------------------------------------------|
| `[0] - domain`         | list    | Domain(s) with conditions applied to records of the model               |

## `kwargs` structure

| Name                   | Type    | Description                                                             |
|------------------------|---------|-------------------------------------------------------------------------|
| `fields`               | list    | Order fields to read (e.g. id, name, loan_id, customer_ID, vin_sn, debtor_code, invoice_status, state)   |

## Response

The method returns a JSON object as a response:

| Name                 | Type    | Description                                                               |
|----------------------|---------|---------------------------------------------------------------------------|
|                      | object  | Order list fields                                                         |

## Request Example

```json
{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "sale.order",
        "method": "get_orders_to_invoice",
        "args": [
            [
                ["partner_id.id", "in", [42, 43]], // ids for the partners to filter
                ["state", "not in", ["draft", "cancel"]],
                ["invoice_status", "=", "to invoice"],
                ["loan_id", "=", "234567890"],
                ["customer_ID", "=", "234567876"],
                ["vin_sn", "=", "4567890"],
                ["debtor_code", "=", "2132"]
            ]
        ],
        "kwargs": {
            "fields": [ // fields to return
                "id",
                "name",
                "loan_id",
                "customer_ID",
                "vin_sn", 
                "debtor_code",
                "invoice_status",
                "state"
            ],
            "limit": 1, // optional params
            "offset": 0,
            "order": "id asc",
            "context": {}
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
            "id": 7,
            "name": "S00007",
            "loan_id": "234567890",
            "customer_ID": "234567876",
            "vin_sn": "4567890",
            "debtor_code": "2132",
            "invoice_status": "to invoice",
            "state": "sale"
        }
    ]
}
```

## cURL Example

```bash
curl --location 'http://localhost:8069/web/dataset/call_kw/sale.order/get_orders_to_invoice' \
--header 'Content-Type: application/json' \
--header 'Cookie: frontend_lang=en_US; session_id=8ace287e798f31740242c2a1cdbe8b45352d7e72' \
--data-raw '{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "sale.order",
        "method": "get_orders_to_invoice",
        "args": [
            [
                ["partner_id.id", "in", [42, 43]],
                ["state", "not in", ["draft", "cancel"]],
                ["invoice_status", "=", "to invoice"],
                ["loan_id", "=", "234567890"],
                ["customer_ID", "=", "234567876"],
                ["vin_sn", "=", "4567890"],
                ["debtor_code", "=", "2132"]
            ]
        ],
        "kwargs": {
            "fields": [ 
                "id",
                "name",
                "loan_id",
                "customer_ID",
                "vin_sn", 
                "debtor_code",
                "invoice_status",
                "state"
            ],
            "limit": 1, 
            "offset": 0,
            "order": "id asc",
            "context": {}
        }
    }
}'
```
