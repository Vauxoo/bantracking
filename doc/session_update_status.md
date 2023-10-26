Update Status of a Sale Order Using Session
===========================================

This endpoint allows to update the Sales order status to be confirmed `(sale)` or cancelled `(cancel)`.

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

## URL

```
POST /web/dataset/call_kw/sale.order/action_confirm
```

Postman Demo: [session_update_status_confirmed_in_sale_order.json](postman_collection.json)

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
        "model": "sale.order",
        "method": "action_confirm",
        "args": [
            67 // sale order id
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
    "result": true
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
        "method": "action_confirm",
        "args": [
            67
        ],
        "kwargs": {}
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

Postman Demo: [session_update_status_cancelled_in_sale_order.json](postman_collection.json)

## Input Parameters

The method expects to receive a python object in the request body with the following parameters:

| Name        | Type          | Description                                                                  |
|-------------|---------------|------------------------------------------------------------------------------|
|             | list          | Ids for the objects to be updated                                            |

## Response

The method returns a JSON object as a response:

| Name                 | Type    | Description                                                               |
|----------------------|---------|---------------------------------------------------------------------------|
|                      | boolean | True if the status is cancelled                                           |

## Request Example

```json
{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "sale.order",
        "method": "cancel_order",
        "args": [
            7 // A sale order id
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
    "result": true
}
```

## cURL Example

```bash
curl --location 'http://localhost:55001/web/dataset/call_kw/sale.order/cancel_order' \
--header 'Content-Type: application/json' \
--header 'Cookie: frontend_lang=en_US; session_id=8ace287e798f31740242c2a1cdbe8b45352d7e72' \
--data '{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "sale.order",
        "method": "cancel_order",
        "args": [
            7 
        ],
        "kwargs": {}
    }
}'
```
