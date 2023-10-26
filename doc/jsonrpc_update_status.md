Update Status of a Sale Order Using Jsonrpc
===========================================

This endpoint allows to update the Sales order status to be confirmed `(sale)` or cancelled `(cancel)`.

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

Confirm a sale order in your database
-----------------------------------

1. Use `action_confirm` method on a database to confirm a sale order with authenticated user in Odoo, this will change the invoice_status of the sale order to be "to invoice":

    ```python
    def action_confirm(self):
        """ Confirm the given quotation(s) and set their confirmation date.

        If the corresponding setting is enabled, also locks the Sale Order.

        :return: True
        :rtype: bool
        :raise: UserError if trying to confirm locked or cancelled SO's
        """
        if self._get_forbidden_state_confirm() & set(self.mapped('state')):
            raise UserError(_(
                "It is not allowed to confirm an order in the following states: %s",
                ", ".join(self._get_forbidden_state_confirm()),
            ))

        self.order_line._validate_analytic_distribution()

        for order in self:
            if order.partner_id in order.message_partner_ids:
                continue
            order.message_subscribe([order.partner_id.id])

        self.write(self._prepare_confirmation_values())

        # Context key 'default_name' is sometimes propagated up to here.
        # We don't need it and it creates issues in the creation of linked records.
        context = self._context.copy()
        context.pop('default_name', None)

        self.with_context(context)._action_confirm()
        if self.env.user.has_group('sale.group_auto_done_setting'):
            self.action_done()

        return True
    ```

Postman Demo: [jsonrpc_update_status_confirmed_in_sale_order.json](postman_collection.json)

## Input Parameters

The method expects to receive a python object in the request body with the following parameters:

| Name        | Type          | Description                                                                  |
|-------------|---------------|------------------------------------------------------------------------------|
|             | list          | Ids for the objects to be confirmed                                          |

## Response

The method returns a JSON object as a response:

| Name                 | Type    | Description                                                               |
|----------------------|---------|---------------------------------------------------------------------------|
|                      | boolean | Indicates whether the status has been updated to "to invoice" (True).     |

## Request Example

```json
{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "service": "object",
        "method": "execute_kw",
        "args": [
            "confisa", //DB
            2, // uid
            "admin", //password or token does not require previous login
            "sale.order", // model
            "action_confirm", // public method
            [ // args
                67 // sale order id
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
        "method": "execute_kw",
        "args": [
            "confisa", 
            2, 
            "admin", 
            "sale.order", 
            "action_confirm", 
            [ 
                67
            ],
            {
                "context": {}
            }
        ]
    }
}'
```

Cancel a sale order in your database
-----------------------------------

1. Use `cancel_order` method on a database to Cancel a sale order with authenticated user in Odoo, this will change the invoice_status of the sale order to be "no", meaning that it is not invoiceable, since the state of the sale order is "cancel":

    ```python
    def cancel_order(self):
        """Cancel a Sale Order.
        Returns: True
        """
        return self._action_cancel()
    ```


Postman Demo: [jsonrpc_update_status_cancelled_in_sale_order.json](postman_collection.json)

## Input Parameters

The method expects to receive a python object in the request body with the following parameters:

| Name        | Type          | Description                                                                  |
|-------------|---------------|------------------------------------------------------------------------------|
| `self`      | Python object | sale.order object.                                                           |

## Response

The method returns a JSON object as a response:

| Name                 | Type    | Description                                                               |
|----------------------|---------|---------------------------------------------------------------------------|
| `jsonrpc`            | string  | Jsonrpc version.                                                          |
| `id`                 | int     | Request id.                                                               |
| `result`             | boolean | True                                                                      |

## Request Example

```json
{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "service": "object",
        "method": "execute_kw",
        "args": [
            "confisa", //DB
            2, // uid
            "admin", //password or token does not require previous login
            "sale.order", // model
            "cancel_order", // public method
            [ // args
                7 // A sale order id
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
        "method": "execute_kw",
        "args": [
            "confisa", 
            2, 
            "admin", 
            "sale.order", 
            "cancel_order", 
            [ 
                7
            ],
            {
                "context": {}
            }
        ]
    }
}'
```
