Get Data for Invoices related with Sale orders
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

Postman Demo: [jsonrpc_get_invoices_info.json](postman_collection.json)

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

Get Data for Invoices related to Sale Orders filtering by debtor_code or invoice_date
-------------------------------------

1. Use `get_invoices_info` method on a database to get the invoice information related to sale orders with authenticated user in Odoo:

    ```python
    @api.model
    def get_invoices_info(self, values):
        """Retrieve invoice information related with data from sale orders.
        Returns:
            dict: A dictionary containing information for each record.
        """
        return self._get_invoices_info(values)
    ```

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

| Name        | Type    | Description                                                                        |
|-------------|---------|------------------------------------------------------------------------------------|
| `values`    | dict    | Sale order values.                                                                 |

## `Values` structure

| Name                            | Type              | Description                                          |
|---------------------------------|-------------------|------------------------------------------------------|
| `debtor_code`                   | string            | Debtor Code                                          |
| `invoice_date`                  | string            | Indicates the Invoice Date (eg. "2023-07-22")        |

## Response

The method returns a JSON object as a response:

| Name                         | Type | Description                                                          |
|------------------------------|------|----------------------------------------------------------------------|
| `CorporationLeasingInvoices` | list | Sale order and Invoice data (id, partner_id, debtor, debtor_code, invoice_id, invoice_name, invoice_date, invoice_status, vin_ns, vat, loan_id, customer_name, identity_document_type, customer_ID, products).  |

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
            "sale.order",
            "get_invoices_info",
            [
                {
                    "debtor_code": "2132",
                    "invoice_date": "2023-07-25"
                }
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
    "result": {
        "CorporationLeasingInvoices": [
            {
                "id": 36,
                "name": "S00036",
                "partner_id": 12,
                "debtor": "Corporación de Crédito Leasing Confisa, S. A.",
                "debtor_code": "2132", // order name
                "invoices": [
                    {
                        "id": 77,
                        "name": "INV/2023/00011",
                        "invoice_date": "2023-07-12"
                    },
                    {
                        "id": 78,
                        "name": "/",
                        "invoice_date": "2023-07-15"
                    }
                ],
                "invoice_status": "invoiced",
                "vin_ns": "1234", // chassis
                "vat": "1-01-09009-1", // rnc
                "loan_id": "123",
                "customer_name": "Sammy",
                "identity_document_type": "passport",
                "customer_ID": "1234",
                "products": [
                    {
                        "id": 50,
                        "name": "PLAN AVAL MENSUAL",
                        "type": "service",
                        "price": 550.85,
                        "quantity": 1.0
                    },
                    {
                        "id": 51,
                        "name": "EV26",
                        "type": "product",
                        "price": 0.0,
                        "quantity": 1.0
                    }
                ]
            }
        ]
    }
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
            "sale.order",
            "get_invoices_info",
            [
                {
                    "debtor_code": "2132",
                    "invoice_date": "2023-07-25"
                }
            ],
            {
                "context": {}
            }
        ]
    }
}'
```
