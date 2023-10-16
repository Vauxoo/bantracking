Get Info for the Installed Services Using Session
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

Postman Demo: [session_get_installed_services.json](postman_collection.json)

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

| Name        | Type    | Description                                                                   |
|-------------|---------|-------------------------------------------------------------------------------|
| `model`     | string  | The name of the Odoo model on which the method will be executed.              |
| `method`    | string  | The name of the public method to be called on the specified model.            |
| `args`      | list    | The positional arguments of the method, provided as a JSON-encoded list.      |
| `kwargs`    | dict    | The keyword arguments of the method, provided as a JSON-encoded object.       |

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

## URL

```
POST /web/dataset/call_kw/sale.order/get_installed_services
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
        "model": "sale.order",
        "method": "get_installed_services",
        "args": [
            {
                "loan_id": "1222",
                "customer_ID": "34878",
                "vin_sn": "4567890",
                "debtor_code": "2132"
            }
        ],
        "kwargs": {
            "offset": 0,
            "limit": 2,
            "order": "id desc",
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
            "name": "S00043",
            "id": 43
        }
    ]
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
        "method": "get_installed_services",
        "args": [
            {
                "loan_id": "1222",
                "customer_ID": "34878",
                "vin_sn": "4567890",
                "debtor_code": "2132"
            }
        ],
        "kwargs": {
            "offset": 0,
            "limit": 2,
            "order": "id desc",
            "context": {}
        }
    }
}'
```
