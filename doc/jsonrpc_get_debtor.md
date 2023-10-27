Get Debtors Using Jsonrpc
===========================================

This controller obtains information from Banco Confisa and Leasing Confisa, such as the debtor, debtor code, rnc and the company code in the Web Service. Only the request should be made to the endpoint without sending any aditional parameters.

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

Postman Demo: [jsonrpc_get_debtor.json](postman_collection.json)

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

1. Use `get_debtor` method on a database to get the installed services with authenticated user in Odoo:

    ```python
    @api.model
    def get_debtor(self, offset=0, limit=None, order=None):
        """Get debtor information for Confisa partners.
        Returns:
            list: A list of dictionaries, each representing a Confisa partner's debtor information.
        """
        confisa_partners = self.get_confisa_partners(offset, limit, order)
        payload = []
        for partner in confisa_partners:
            payload.append(
                {
                    "codigoDeudor": partner.debtor_code,
                    "deudor": partner.name,
                    "rncDeudor": partner.vat,
                    "codigoCompania": partner.id,
                }
            )
        return payload

    ```

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

## `args` structure

No input values are required into the args list.

## `kwargs` structure

| Name                   | Type    | Description                                                             |
|------------------------|---------|-------------------------------------------------------------------------|
| `offset`               | integer | The offset for the records to retrieve                                  |
| `limit`                | integer | Maximum limit of records to retrieve                                    |
| `order`                | string  | Specifies the ordering of the results for a specific field. Use `asc` for ascending order or `desc` for descending order. (e.g. "id asc") |

## Response

The method returns a JSON object as a response:

| Name                 | Type    | Description                                                               |
|----------------------|---------|---------------------------------------------------------------------------|
|                      | list    | List of dictionaries, each representing a Confisa partner's debtor info.  |

## Request Example

```json
{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "service": "object",
        "method": "execute_kw",
        "args": [
            "db_name", // database
            2, // uid
            "admin", // password or token
            "res.partner", // model
            "get_debtor", // public method
            [],
            {
                "offset": 0,
                "limit": 3,
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
            "codigoDeudor": "2132",
            "deudor": "Banco de Ahorro y Crédito Confisa, S. A.",
            "rncDeudor": "1-01-09009-1",
            "codigoCompania": 43
        },
        {
            "codigoDeudor": false,
            "deudor": "Corporación de Crédito Leasing Confisa, S. A.",
            "rncDeudor": "1-01-15065-3",
            "codigoCompania": 44
        }
    ]
}
```

## cURL Example

```bash
curl --location 'http://localhost:55001/jsonrpc' \
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
            "res.partner", 
            "get_debtor"
            [],
            {
                "offset": 0,
                "limit": 3,
                "order": "id asc",
                "context": {}
            }
        ]
    }
}'
```
