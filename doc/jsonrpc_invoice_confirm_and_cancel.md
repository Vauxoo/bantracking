Confirm or Cancel an Invoice Using Jsonrpc
===============================

Call a method
--------------

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

Postman Demo: [jsonrpc_confirm_invoice.json](postman_collection.json) and [jsonrpc_cancel_invoice.json](postman_collection.json)

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
        "service": "object",
        "method": "execute_kw",
        "args": [
            "db_name",
            2,
            "admin",
            "account.move",
            "action_post",
            [
                [73] // invoice id to be confirmed
            ],
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
    "result": false
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
        "method": "execute_kw",
        "args": [
            "db_name", 
            2, 
            "admin",
            "account.move",
            "action_post",
            [
                [73]
            ],
            {
                "context": {}
            }
        ]
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
        "service": "object",
        "method": "execute_kw",
        "args": [
            "db_name", // DB
            2, // uid
            "admin", // password or token does not require previous login
            "account.move", // model
            "button_cancel", // public method
            [ // args
                [73]
            ],
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
    "id": 16
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
        "method": "execute_kw",
        "args": [
            "db_name", 
            2, 
            "admin", 
            "account.move", 
            "button_cancel", 
            [ 
                [73]
            ],
            {
                "context": {}
            }
        ]
    }
}'
```
