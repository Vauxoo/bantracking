Pinging the service to check its availability Using Session
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

Postman Demo: [session_ping.json](postman_collection.json)

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

| Name        | Type    | Description                                                                   |
|-------------|---------|-------------------------------------------------------------------------------|
| `model`     | string  | The name of the Odoo model on which the method will be executed.              |
| `method`    | string  | The name of the public method to be called on the specified model.            |
| `args`      | list    | The positional arguments of the method, provided as a JSON-encoded list.      |
| `kwargs`    | dict    | The keyword arguments of the method, provided as a JSON-encoded object.       |

Ping the service to check its availability and whether it's receiving requests
-------------------------------------

1. Use `ping` method on a database to validate if the service is active in Odoo:

    ```python
    @api.model
    def ping(self):
        """Check the status of the server.
        Returns:
            Response: A true boolean value indicating the server is up.
        """
        return Response("true", status=200, headers={"Content-Type": "application/json"})
    ```

## URL

```
POST /web/dataset/call_kw/res.users/ping
```

## Input Parameters

No input values are used into the args list.

## Response

The method returns a BOOL as a response:

| Name                  | Type    | Description                                   |
|-----------------------|---------|-----------------------------------------------|
|                       | boolean | A true boolean if the server is up.           |

## Request Example

```json
{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "res.users",
        "method": "ping",
        "args": [
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
    "result": true
}
```

## cURL Example

```bash
curl --location 'http://localhost:55001/web/dataset/call_kw/res.users/ping' \
--header 'Content-Type: application/json' \
--header 'Cookie: frontend_lang=en_US; session_id=8ace287e798f31740242c2a1cdbe8b45352d7e72' \
--data '{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "res.users",
        "method": "ping",
        "args": [
        ],
        "kwargs": {}
    }
}'
```
