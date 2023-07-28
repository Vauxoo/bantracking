Invoice Subscription Using Session
===========================================

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

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

| Name        | Type    | Description                                                                   |
|-------------|---------|-------------------------------------------------------------------------------|
| `model`     | string  | The name of the Odoo model on which the method will be executed.              |
| `method`    | string  | The name of the public method to be called on the specified model.            |
| `args`      | list    | The positional arguments of the method, provided as a JSON-encoded list.      |
| `kwargs`    | dict    | The keyword arguments of the method, provided as a JSON-encoded object.       |

Confirm an invoice subscription in your database
-----------------------------------

1. Use `create_recurring_invoice` method on a database to create an invoice subscription for a sale order with authenticated user in Odoo:

    ```python
    def create_recurring_invoice(self):
        account_move = self._create_recurring_invoice()
        return {"invoice_id": account_move.id, "state": account_move.state}
    ```

## URL

```
POST /web/dataset/call_kw/sale.order/create_recurring_invoice
```

Postman Demo: [session_invoice_subscription.json](postman_collection.json)

## Input Parameters

The method expects to receive a python object in the request body with the following parameters:

| Name        | Type          | Description                                                                  |
|-------------|---------------|------------------------------------------------------------------------------|
|             | list          | Ids for the objects to be confirmed                                          |

## Response

The method returns a JSON object as a response.

## Request Example

```json
{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "sale.order",
        "method": "create_recurring_invoice",
        "args": [
            2 // sale order id
        ],
        "kwargs": {}
    }
}
```

## Response Example

```json
{
    "jsonrpc": "2.0",
    "id": 16,
    "result": {
        "invoice_id": 292,
        "state": "draft"
    }
}
```

## cURL Example

```bash
curl --location 'http://localhost:8069/web/dataset/call_kw/sale.order/action_confirm' \
--header 'Content-Type: application/json' \
--header 'Cookie: frontend_lang=en_US; session_id=8ace287e798f31740242c2a1cdbe8b45352d7e72' \
--data '{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "sale.order",
        "method": "create_recurring_invoice",
        "args": [
            2 
        ],
        "kwargs": {}
    }
}'
```
