Create Sales Orders
===========================================

This endpoint allows to create  new sale order. When using this endpoint, there are some important considerations to keep in mind. First of all, it is mandatory to provide a loan ID with each order you wish to place. This Loan ID is a unique identifier that helps avoid duplicate registrations and ensures that orders are assigned correctly. Secondly, it is important to note that insertion of a Loan ID that has already been previously entered will not be allowed, even if the corresponding order is later cancelled. This helps keep a clean database and avoid confusion in the future.
The initial status of the orders will be "to invoice".

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

Postman Demo: [jsonrpc_create_sale_order.json](postman_collection.json)

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

Create a Sale order in your database
-------------------------------------

1. Use `create_order` method on a database to update Sale Orders with authenticated user in Odoo:

    ```python
    @api.model
    def create_order(self, values):
        """Update the vehicle data for Sale orders.
        Returns:
            dict: A dictionary containing the updated orders.
        """
        return self._create_order(values)
    ```

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

| Name        | Type    | Description                                                                       |
|-------------|---------|-----------------------------------------------------------------------------------|
| `values`    | dict    | Sale order values.                                                                |

## `Values` structure

| Name                            | Type              | Description                                         |
|---------------------------------|-------------------|-----------------------------------------------------|
| `project_id`                    | integer           | Project ID                                          |
| `partner_id`                    | integer           | Partner ID                                          |
| `loan_id`                       | integer           | Loan ID                                             |
| `recurrence_id`                 | integer           | Recurrence ID (Only required if a product of the Sale order has recurrency)          |
| `confisa_admission_date`        | string            | Admission Date to Confisa (e.g. "2023-07-22")        |
| `order_line_ids`                | list              | Dictionary of product ID's                          |
| `identity_document_type`        | string            | Documentation Type (cedula, passport, rnc)          |
| `customer_ID`                   | string            | Indicates the number (ID) of the Identification     |
| `customer_name`                 | string            | Indicates the name associated with the customer_ID  |
| `vehicle_brand_id`              | integer           | ID for the vehicle brand                            |
| `vehicle_model_id`              | integer           | ID for the vehicle model                            |
| `category_id`                   | integer           | ID for the vehicle category                         |
| `color`                         | string            | Vehicle Color                                       |
| `vin_sn`                        | string            | Chassis                                             |
| `license_plate`                 | string            | License Plate of the vehicle                        |
| `manufacturing_year`            | string            | Manufacturing year of the vehicle                   |

## Response

The method returns a JSON object as a response:

| Name                 | Type    | Description                                                              |
|----------------------|---------|--------------------------------------------------------------------------|
| `message`            | string  | Error message                                                            |
| `order`              | list    | Sales order data. (id, name, partner_id, loan_id)                        |

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
            "create_order",
            [
                { 
                    "project_id": 15,
                    "partner_id": 71,
                    "loan_id": 124,
                    "recurrence_id": 4, // set only if the product requires it
                    "confisa_admission_date": "2023-07-22",
                    "order_line_ids": [
                        {
                            "product_id": 50, // Can also use xml id example: product.product_product_50
                            "price_unit": 0.0 // Optional, if it is required to change the default value
                        },
                        {
                            "product_id": 51, // Can also use xml id example: product.product_product_51
                            "price_unit": 0.0 // Optional, if it is required to change the default value
                        }
                    ],
                    "identity_document_type": "cedula",
                    "customer_ID": "34878",
                    "customer_name": "John",
                    "vehicle_brand_id": 4,
                    "vehicle_model_id": 2,
                    "category_id": 3,
                    "color": "Blue",
                    "vin_sn": "4567890",
                    "license_plate": "AD2132",
                    "manufacturing_year": "2021"
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
        "message": "",
        "order": [
            {
                "id": 50,
                "name": "S00050",
                "partner_id": [
                    71,
                    "Corporación de Crédito Leasing Confisa, S. A."
                ],
                "loan_id": "124"
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
            "create_order",
            [
                {
                    "project_id": 15,
                    "partner_id": 71,
                    "loan_id": 124,
                    "recurrence_id": 4,
                    "confisa_admission_date": "2023-07-22",
                    "order_line_ids": [
                        {
                            "product_id": 50
                        },
                        {
                            "product_id": 51
                        }
                    ],
                    "identity_document_type": "cedula",
                    "customer_ID": "34878",
                    "customer_name": "John",
                    "vehicle_brand_id": 4,
                    "vehicle_model_id": 2,
                    "category_id": 3,
                    "color": "Blue",
                    "vin_sn": "4567890",
                    "license_plate": "AD2132",
                    "manufacturing_year": "2021"
                }
            ],
            {
                "context": {}
            }
        ]
    }
}'
```
