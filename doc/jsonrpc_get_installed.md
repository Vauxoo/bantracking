Get Info for the Installed Services
===========================================

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

Postman Demo: [jsonrpc_get_installed_services.json](postman_collection.json)

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

Get info about the installed services in your database
-------------------------------------

1. Use `get_installed_services` method on a database to get the installed services with authenticated user in Odoo:

    ```python
    @api.model
    def get_installed_services(self, values, offset=0, limit=None, order=None):
        """Get the installed items from Confisa.
        Returns:
            list[dict]: A list containing dictionaries of installed item orders.
        """
        return self._get_installed_services(values, offset, limit, order)
    ```

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

| Name        | Type    | Description                                                                        |
|-------------|---------|------------------------------------------------------------------------------------|
| `values`    | dict    | Sale order values.                                                                 |

## `Values` structure

| Name                            | Type              | Description                                                     |
|---------------------------------|-------------------|-----------------------------------------------------------------|
| `loan_id`                       | string            | Loan ID (maxlength=50)                                          |
| `customer_ID`                   | string            | Indicates the number (ID) of the Identification (maxlength=40)  |
| `vin_sn`                        | string            | Chassis (maxlength=50)                                          |
| `debtor_code`                   | string            | Debtor Code (maxlength=50)                                      |

## Response

The method returns a JSON object as a response:

| Name                 | Type    | Description                                                               |
|----------------------|---------|---------------------------------------------------------------------------|
|                      | list    | Installed Orders list                                                     |

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
            "get_installed_services",
            [
                {
                    "loan_id": "1222",
                    "customer_ID": "34878",
                    "vin_sn": "4567890",
                    "debtor_code": "2132"
                }
            ],
            {
                "offset": 0,
                "limit": 2,
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
            "name": "S00043",
            "id": 43
        }
    ]
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
            "get_installed_services",
            [
                {
                    "loan_id": "1222",
                    "customer_ID": "34878",
                    "vin_sn": "4567890",
                    "debtor_code": "2132"
                }
            ],
            {
                "offset": 0,
                "limit": 2,
                "order": "id asc",
                "context": {}
            }
        ]
    }
}'
```
