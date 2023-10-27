Get Sale Orders to invoice
===========================================

This endpoint allows you to retrieve all orders that are not in the `draft` or `cancel` state.

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

Postman Demo: [jsonrpc_get_orders_to_invoice.json](postman_collection.json)

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
POST /jsonrpc
```

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

| Name        | Type    | Description                                                                        |
|-------------|---------|------------------------------------------------------------------------------------|
| `service`   | string  | Type of service. (common, db, object)                                              |
| `method`    | string  | Name of the method to call.                                                        |
| `args`      | list    | The positional arguments of the method, provided as a JSON-encoded list.           |

## `args` structure

| Name                   | Type    | Description                                                             |
|------------------------|---------|-------------------------------------------------------------------------|
| `[0] - db_name`        | string  | Odoo database name                                                      |
| `[1] - uid`            | integer | User ID                                                                 |
| `[2] - password`       | string  | Password or [Token](https://www.odoo.com/documentation/16.0/developer/reference/external_api.html#api-keys)|
| `[3] - model`          | string  | The name of the Odoo model on which the method will be executed.        |
| `[4] - method`         | string  | The name of the public method to be called on the specified model.      |
| `[5] - args`           | list    | The positional arguments of the method, provided as a JSON-encoded list.|
| `[6] - kwargs`         | dict    | The keyword arguments of the method, provided as a JSON-encoded object. |

## Input Parameters for the `[5]-args` list

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
        "service": "object",
        "method": "execute_kw",
        "args": [
            "confisa", // database
            2, // uid
            "admin", // password or token
            "sale.order", // model
            "get_orders_to_invoice", // public method
            [ // args
                [
                    ["partner_id.id", "in", [42, 43]], // the ids for the partners to filter
                    ["state", "not in", ["draft", "cancel"]],
                    ["invoice_status", "=", "to invoice"],
                    ["loan_id", "=", "234567890"],
                    ["customer_ID", "=", "234567876"],
                    ["vin_sn", "=", "4567890"],
                    ["debtor_code", "=", "2132"]
                ] // domain
            ],
            { // kwargs
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
            "sale.order",
            "get_orders_to_invoice",
            [
                [["partner_id.id", "in", [42, 43]], ["state", "not in", ["draft", "cancel"]], ["invoice_status", "=", "to invoice"], ["loan_id", "=", "234567890"], ["customer_ID", "=", "234567876"], ["vin_sn", "=", "4567890"], ["debtor_code", "=", "2132"]]
            ],
            {
                "fields": ["name", "loan_id", "customer_ID", "vin_sn", "debtor_code", "invoice_status", "state"],
                "limit": 1,
                "offset": 0,
                "order": "id asc",
                "context": {}
            }
        ]
    }
}'
```
