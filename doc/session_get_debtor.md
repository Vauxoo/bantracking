Get Debtors Using Session
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

Postman Demo: [session_get_debtor.json](postman_collection.json)

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

1. Use `get_debtor` method on a database to get the installed services with authenticated user in Odoo:

    ```python
    @api.model
    def get_debtor(self):
        """
        Get debtor information for Confisa partners.
        Returns:
            list: A list of dictionaries, each representing a Confisa partner's debtor information.
        """
        confisa_partners = self.get_confisa_partners()
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

## URL

```
POST /web/dataset/call_kw/res.partner/get_debtor
```

## Input Parameters

No input values are required into the args list.

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
        "model": "res.partner",
        "method": "get_debtor",
        "args": [],
        "kwargs": {}
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
curl --location 'http://localhost:55001/web/dataset/call_kw/res.partner/get_debtor' \
--header 'Content-Type: application/json' \
--header 'Cookie: session_id=7755387015f5e1fc2b6c9c2e602100ea19938483' \
--data '{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "res.partner",
        "method": "get_debtor",
        "args": [],
        "kwargs": {}
    }
}'
```
