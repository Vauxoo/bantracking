Get Info for the Active Services Using Session
===========================================

This controller allows you to obtain information about all active services of Banco Confisa and Leasing Confisa after logging in and having a valid token. It also allows applying various filters to refine the information returned by the service.

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

Postman Demo: [session_get_services.json](postman_collection.json)

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

| Name        | Type    | Description                                                                   |
|-------------|---------|-------------------------------------------------------------------------------|
| `model`     | string  | The name of the Odoo model on which the method will be executed.              |
| `method`    | string  | The name of the public method to be called on the specified model.            |
| `args`      | list    | The positional arguments of the method, provided as a JSON-encoded list.      |
| `kwargs`    | dict    | The keyword arguments of the method, provided as a JSON-encoded object.       |

Get info about the active services in your database
-------------------------------------

1. Use `get_services` method on a database to get the service information with authenticated user in Odoo:

    ```python
    @api.model
    def get_services(self, values, offset=0, limit=None, order=None):
        """Retrieve service information.
        Returns:
            dict: A dictionary containing service information for each order.
        """
        return self._get_services(values, offset, limit, order)
    ```

## URL

```
POST /web/dataset/call_kw/sale.order/get_services
```

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

| Name        | Type    | Description                                                                        |
|-------------|---------|------------------------------------------------------------------------------------|
| `values`    | dict    | Sale order values.                                                                 |

## `Values` structure

| Name                            | Type              | Description                                                |
|---------------------------------|-------------------|------------------------------------------------------------|
| `loan_id`                       | string            | Loan ID (Optional)                                                   |
| `customer_ID`                   | string            | Indicates the number (ID) of the Identification (Optional)           |
| `vat`                           | string            | Indicates the Debtor RNC (Optional)                                  |
| `vin_sn`                        | string            | Chassis (Optional)                                                   |
| `debtor_code`                   | string            | Debtor Code (Optional)                                               |
| `invoice_date`                  | string            | Indicates the Invoice Date (full date is accepted, but only the month and year will be considered for the filter, e.g. "2023-07-25") (Optional)             |
| `confisa_admission_date`        | string            | Indicates the Admission Date to Confisa (full date is accepted, but only the month and year will be considered for the filter, e.g. "2023-07-25") (Optional)|

## Response

The method returns a JSON object as a response:

| Name                           | Type    | Description                                                     |
|--------------------------------|---------|-----------------------------------------------------------------|
|                                | list    | List of dictionaries with Sales order data. (id, name, partner_id, debtor, debtor_code, invoice_date, invoice_status, service_status, vehicle_desciption, vin_ns, vat, loan_id, customer_name, identity_document_type, confisa_admission_date, products)         |

## Request Example

```json
{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "sale.order",
        "method": "get_services",
        "args": [
            {
                "loan_id": "124",
                "customer_ID": "34878",
                "vat": "1-01-09009-1",
                "vin_sn": "4567890",
                "debtor_code": "2132",
                "invoice_date": "2023-07-25",
                "confisa_admission_date": "2023-07-13"
            }
        ],
        "kwargs": {
            "offset": 0,
            "limit": 1,
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
            "id": 127,
            "name": "S00001", // order name
            "partner_id": 10, // company_id or partner_id
            "debtor": "Deco Addict",
            "debtor_code": "2132",
            "invoice": [
                {
                    "invoice_name": "INV/2023/00013",
                    "invoice_date": "2023-07-27"
                }
            ],
            "invoice_status": "to invoice",
            "service_status": "ACTIVE",
            "vehicle_description": "Bmw Serie 1 COLOR red PLATE QW242 YEAR 2022",
            "vin_ns": "4567890", // Chassis
            "vat": "1-01-09009-1", // RNC
            "loan_id": "124",
            "customer_name": "John",
            "customer_ID": "34878",
            "identity_document_type": "cedula",
            "confisa_admission_date": "2023-07-17",
            "products": [
                {
                    "id": 141,
                    "name": "PLAN AVAL MENSUAL",
                    "type": "service",
                    "price": 550.85,
                    "quantity": 1.0,
                },
                {
                    "id": 212,
                    "name": "EV26",
                    "type": "product",
                    "price": 0.0,
                    "quantity": 1.0,
                }
            ],
        }
    ]
}
```

## cURL Example

```bash
curl --location 'http://localhost:8069/web/dataset/call_kw/sale.order/get_services' \
--header 'Content-Type: application/json' \
--header 'Cookie: frontend_lang=en_US; session_id=8ace287e798f31740242c2a1cdbe8b45352d7e72' \
--data '{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "sale.order",
        "method": "get_services",
        "args": [
            {
                "loan_id": "124",
                "customer_ID": "34878",
                "vat": "1-01-09009-1",
                "vin_sn": "4567890",
                "debtor_code": "2132",
                "invoice_date": "2023-07-25",
                "confisa_admission_date": "2023-07-13"
            }
        ],
        "kwargs": {
            "offset": 0,
            "limit": 1,
            "order": "id asc",
            "context": {}
        }
    }
}'
```
