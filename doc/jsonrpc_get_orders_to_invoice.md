Get Sale Orders to invoice
===========================================

Call a method
-------------

1. You can use `jsonrpc` to execute specific Odoo methods with JSON-RPC and receive the results back in JSON format:

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

1. Use `search_read` method on a database to get the Sale orders to invoice with authenticated user in Odoo:

    ```python
    @api.model
    def search_read(self, domain=None, fields=None, offset=0, limit=None, order=None, **read_kwargs):
        """Perform a :meth:`search` followed by a :meth:`read`.

        :param domain: Search domain, see ``args`` parameter in :meth:`search`.
            Defaults to an empty domain that will match all records.
        :param fields: List of fields to read, see ``fields`` parameter in :meth:`read`.
            Defaults to all fields.
        :param int offset: Number of records to skip, see ``offset`` parameter in :meth:`search`.
            Defaults to 0.
        :param int limit: Maximum number of records to return, see ``limit`` parameter in :meth:`search`.
            Defaults to no limit.
        :param order: Columns to sort result, see ``order`` parameter in :meth:`search`.
            Defaults to no sort.
        :param read_kwargs: All read keywords arguments used to call
            ``read(..., **read_kwargs)`` method e.g. you can use
            ``search_read(..., load='')`` in order to avoid computing name_get
        :return: List of dictionaries containing the asked fields.
        :rtype: list(dict).
        """
        records = self.search(domain or [], offset=offset, limit=limit, order=order)
        if not records:
            return []

        if fields and fields == ['id']:
            # shortcut read if we only want the ids
            return [{'id': record.id} for record in records]

        # read() ignores active_test, but it would forward it to any downstream search call
        # (e.g. for x2m or function fields), and this is not the desired behavior, the flag
        # was presumably only meant for the main search().
        # TODO: Move this to read() directly?
        if 'active_test' in self._context:
            context = dict(self._context)
            del context['active_test']
            records = records.with_context(context)

        result = records.read(fields, **read_kwargs)
        if len(result) <= 1:
            return result

        # reorder read
        index = {vals['id']: vals for vals in result}
        return [index[record.id] for record in records if record.id in index]
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
| `fields`               | list    | Order fields to read (eg. id, name, loan_id, customer_ID, vin_sn, debtor_code, invoice_status, state)   |

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
            "search_read", // public method
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
                "limit": 1, // optional params in the search_read method
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
--header 'Cookie: session_id=66105dfbe9ebf21701026c0e354b0e8d5a291494' \
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
            "search_read",
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