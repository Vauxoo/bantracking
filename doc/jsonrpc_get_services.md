Get Info for the Active Services
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

Postman Demo: [jsonrpc_get_services.json](postman_collection.json)

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

Get info about the active services in your database
-------------------------------------

1. Use `get_services` method on a database to get the service information with authenticated user in Odoo:

    ```python
    @api.model
    def get_services(self, values):
        """Retrieve service information.
        Returns:
            dict: A dictionary containing service information for each order.
        """
        return self._get_services(values)
    ```

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

| Name        | Type    | Description                                                                        |
|-------------|---------|------------------------------------------------------------------------------------|
| `values`    | dict    | Sale order values.                                                                 |

## `Values` structure

| Name                            | Type              | Description                                                |
|---------------------------------|-------------------|------------------------------------------------------------|
| `loan_id`                       | string            | Loan ID                                                    |
| `customer_ID`                   | string            | Indicates the number (ID) of the Identification            |
| `vat`                           | string            | Indicates the Debtor RNC                                   |
| `vin_sn`                        | string            | Chassis                                                    |
| `debtor_code`                   | string            | Debtor Code                                                |
| `invoice_date`                  | string            | Indicates the Invoice Date (eg. "2023-07-25")              |
| `confisa_admission_date`        | string            | Indicates the Admission Date to Confisa (eg. "2023-07-25") |

## Response

The method returns a JSON object as a response:

| Name                           | Type    | Description                                                     |
|--------------------------------|---------|-----------------------------------------------------------------|
| `CorporationLeasingServices`   | list    | Sales order data. (id, name, partner_id, debtor, debtor_code, invoice_date, invoice_status, service_status, vehicle_desciption, vin_ns, vat, loan_id, customer_name, identity_document_type, confisa_admission_date, products)         |

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
            "get_services",
            [
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
        "CorporationLeasingServices": [
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
            "get_services",
            [
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
            {
                "context": {}
            }
        ]
    }
}'
```