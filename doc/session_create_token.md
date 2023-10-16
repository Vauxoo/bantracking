Create Token for authentication Using Session
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

Postman Demo: [session_create_token.json](postman_collection.json)

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

| Name        | Type    | Description                                                                   |
|-------------|---------|-------------------------------------------------------------------------------|
| `model`     | string  | The name of the Odoo model on which the method will be executed.              |
| `method`    | string  | The name of the public method to be called on the specified model.            |
| `args`      | list    | The positional arguments of the method, provided as a JSON-encoded list.      |
| `kwargs`    | dict    | The keyword arguments of the method, provided as a JSON-encoded object.       |

Generate a valid Token in your database
-------------------------------------

1. Use `get_token` method on a database to get a token with an authenticated user in Odoo:

    ```python
    @api.model
    def get_token(self):
        """Method to retrieve the generated token.
        """
        return self._get_token()
    ```

## URL

```
POST /web/dataset/call_kw/res.users/get_token
```

## Input Parameters

No input values are required into the args list.

## Response

The method returns a STR as a response:

| Name                  | Type    | Description                                   |
|-----------------------|---------|-----------------------------------------------|
|                       | string  | Token for the authenticated user in Odoo.     |

## Request Example

```json
{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "res.users",
        "method": "get_token",
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
    "result": "e46d9a8cfa843f4fe4bfb4e2a3f07d3aff309892" // Token
}
```

## cURL Example

```bash
curl --location 'http://localhost:8069/web/dataset/call_kw/res.users/get_token' \
--header 'Content-Type: application/json' \
--header 'Cookie: frontend_lang=en_US; session_id=c2db48b21537fd2953e249f6d7a56ebd0ba11ab3' \
--data '{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "res.users",
        "method": "get_token",
        "args": [],
        "kwargs": {}
    }
}'
```
