Confirm or Cancel an Invoice Using Session
===============================

This endpoint allows to update the invoice status to be confirmed `(posted)` or cancelled `(cancel)`.

Call a method
--------------

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

Postman Demo: [session_confirm_invoice.json](postman_collection.json) and [session_cancel_invoice.json](postman_collection.json)

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

| Name        | Type    | Description                                                                   |
|-------------|---------|-------------------------------------------------------------------------------|
| `model`     | string  | The name of the Odoo model on which the method will be executed.              |
| `method`    | string  | The name of the public method to be called on the specified model.            |
| `args`      | list    | The positional arguments of the method, provided as a JSON-encoded list.      |
| `kwargs`    | dict    | The keyword arguments of the method, provided as a JSON-encoded object.       |

Confirm an invoice in your database
----------------------------------

1. Use `action_post` method on a database to confirm an invoice with authenticated user in Odoo:

    ```python
    def action_post(self):
        moves_with_payments = self.filtered('payment_id')
        other_moves = self - moves_with_payments
        if moves_with_payments:
            moves_with_payments.payment_id.action_post()
        if other_moves:
            other_moves._post(soft=False)
        return False
    ```

## URL

```
POST /web/dataset/call_kw/account.move/action_post
```

## Input Parameters

The method expects to receive a python object in the request body with the following parameters:

| Name        | Type             | Description                                                               |
|-------------|------------------|---------------------------------------------------------------------------|
|             | list             | Ids for the objects to be invoiced                                        |

## Response

The method returns a JSON object as a response:

| Name                 | Type    | Description                                                               |
|----------------------|---------|---------------------------------------------------------------------------|
|                      | boolean | A false boolean if the object was confirmed to be invoiced.               |

## Request Example

```json
{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "account.move",
        "method": "action_post",
        "args": [
            [73] // invoice id to be confirmed
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
    "result": false
}
```

## cURL Example

```bash
curl --location 'http://localhost:8069/web/dataset/call_kw/account.move/action_post' \
--header 'Content-Type: application/json' \
--header 'Cookie: frontend_lang=en_US; session_id=8ace287e798f31740242c2a1cdbe8b45352d7e72' \
--data '{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "account.move",
        "method": "action_post",
        "args": [
            [73] 
        ],
        "kwargs": {}
    }
}'
```

Cancel an invoice in your database
----------------------------------

1. Use `button_cancel` method on a database to cancel an invoice with authenticated user in Odoo:

    ```python
    def button_cancel(self):
        self.write({'auto_post': 'no', 'state': 'cancel'})
    ```

## Input Parameters

The method expects to receive a python object in the request body with the following parameters:

| Name        | Type             | Description                                                               |
|-------------|------------------|---------------------------------------------------------------------------|
|             | list             | Ids for the objects to be cancelled                                       |

## Response

The method returns a JSON object as a response:

| Name                 | Type    | Description                                                               |
|----------------------|---------|---------------------------------------------------------------------------|
| `jsonrpc`            | string  | Jsonrpc version.                                                          |
| `id`                 | integer | Request id.                                                               |

## Request Example

```json
{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "account.move",
        "method": "button_cancel",
        "args": [
            [73]
        ],
        "kwargs": {}
    }
}
```

## Response Example

```json
{
    "jsonrpc": "2.0",
    "id": 16
}
```

## cURL Example

```bash
curl --location 'http://localhost:8069/web/dataset/call_kw/account.move/button_cancel' \
--header 'Content-Type: application/json' \
--header 'Cookie: frontend_lang=en_US; session_id=8ace287e798f31740242c2a1cdbe8b45352d7e72' \
--data '{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "account.move",
        "method": "button_cancel",
        "args": [
            [73]
        ],
        "kwargs": {}
    }
}'
```
