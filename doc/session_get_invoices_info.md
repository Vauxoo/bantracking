Get Data for Invoices related with Sale orders Using Session
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

Postman Demo: [session_get_invoices_info.json](postman_collection.json)

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

| Name        | Type    | Description                                                                   |
|-------------|---------|-------------------------------------------------------------------------------|
| `model`     | string  | The name of the Odoo model on which the method will be executed.              |
| `method`    | string  | The name of the public method to be called on the specified model.            |
| `args`      | list    | The positional arguments of the method, provided as a JSON-encoded list.      |
| `kwargs`    | dict    | The keyword arguments of the method, provided as a JSON-encoded object.       |

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

## URL

```
POST /web/dataset/call_kw/sale.order/get_invoices_info
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
| `invoice_date`                  | string            | Indicates the Invoice Date (eg. "2023-07-25")        |

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
        "model": "sale.order",
        "method": "get_invoices_info",
        "args": [
            {
                "debtor_code": "2132",
                "invoice_date": "2023-07-25"
            }
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
curl --location 'http://localhost:8069/web/dataset/call_kw/sale.order/get_installed_services' \
--header 'Content-Type: application/json' \
--header 'Cookie: frontend_lang=en_US; session_id=8ace287e798f31740242c2a1cdbe8b45352d7e72' \
--data '{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "sale.order",
        "method": "get_invoices_info",
        "args": [
            {
                "debtor_code": "2132",
                "invoice_date": "2023-07-25"
            }
        ],
        "kwargs": {}
    }
}'
```
