Read Vehicle info Using Session
=============================

This endpoints allows to retrieve info to look for the vehicle brands, vehicle models and vehicle categories to be used to create or update a Sales order. 

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

Postman Demo: [session_read_vehicle_brand_model_category.json](postman_collection.json)

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

| Name        | Type    | Description                                                                   |
|-------------|---------|-------------------------------------------------------------------------------|
| `model`     | string  | The name of the Odoo model on which the method will be executed.              |
| `method`    | string  | The name of the public method to be called on the specified model.            |
| `args`      | list    | The positional arguments of the method, provided as a JSON-encoded list.      |
| `kwargs`    | dict    | The keyword arguments of the method, provided as a JSON-encoded object.       |

Read Vehicle brands in your database
=============================

1. Use `get_vehicle_model_brand` method on a database to read vehicle brands and their fields using domains:

    ```python
    @api.model
    def get_vehicle_model_brand(self, offset=0, limit=None):
        """Get model brand."""
        return self._get_vehicle_model_brand(offset, limit)
    ```

## URL

```
POST /web/dataset/call_kw/fleet.vehicle.model.brand/get_vehicle_model_brand
```

Postman Demo: [session_read_vehicle_brand_model_category.json](postman_collection.json)

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

## `args` structure

No input values are required into the args list.

## `kwargs` structure

| Name                   | Type    | Description                                                             |
|------------------------|---------|-------------------------------------------------------------------------|
| `offset`               | integer | The offset for the records to retrieve                                  |
| `limit`                | integer | Maximum limit of records to retrieve                                    |

## Response

The method returns a JSON object as a response:

| Name                 | Type    | Description                                                              |
|----------------------|---------|--------------------------------------------------------------------------|
|                      | list    | Vehicle list data                                                        |

## Request Example

```json
{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "fleet.vehicle.model.brand",
        "method": "get_vehicle_model_brand",
        "args": [],
        "kwargs": {
            "offset": 0,
            "limit": 3
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
            "id": 4,
            "name": "Audi",
            "model_ids": [
                13,
                14,
                15,
                16,
                17,
                18,
                19,
                20,
                21,
                22,
                23
            ]
        },
        {
            "id": 5,
            "name": "Austin",
            "model_ids": []
        },
        {
            "id": 52,
            "name": "Renault",
            "model_ids": []
        }
    ]
}
```

## cURL Example

```bash
curl --location 'http://localhost:8069/web/dataset/call_kw/fleet.vehicle.model.brand/get_vehicle_model_brand' \
--header 'Content-Type: application/json' \
--header 'Cookie: frontend_lang=en_US; session_id=8ace287e798f31740242c2a1cdbe8b45352d7e72' \
--data '{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "fleet.vehicle.model.brand",
        "method": "get_vehicle_model_brand",
        "args": [],
        "kwargs": {
            "offset": 0,
            "limit": 3
        }
    }
}'
```

Read vehicle models in your database
=============================

1. Use `get_vehicle_model` method on a database to read a vehicle record and their fields:

    ```python
    @api.model
    def get_vehicle_model(self, offset=0, limit=None):
        """Get Vehicle Model."""
        return self._get_vehicle_model(offset, limit)
    ```

## URL

```
POST /web/dataset/call_kw/fleet.vehicle.model/get_vehicle_model
```

Postman Demo: [session_read_vehicle_brand_model_category.json](postman_collection.json)

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

## `args` structure

No input values are required into the args list.

## `kwargs` structure

| Name                   | Type    | Description                                                             |
|------------------------|---------|-------------------------------------------------------------------------|
| `offset`               | integer | The offset for the records to retrieve                                  |
| `limit`                | integer | Maximum limit of records to retrieve                                    |

## Response

The method returns a JSON object as a response:

| Name                 | Type    | Description                                                               |
|----------------------|---------|---------------------------------------------------------------------------|
|                      | list    | Vehicle data                                                              |

## Request Example to get models of a vehicle related with a brand

```json
{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "fleet.vehicle.model",
        "method": "get_vehicle_model",
        "args": [],
        "kwargs": {
            "offset": 0,
            "limit": 1
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
            "id": 13,
            "name": "A1",
            "brand_id": [
                4,
                "Audi"
            ]
        }
    ]
}
```

## cURL Example

```bash
curl --location 'http://localhost:8069/web/dataset/call_kw/fleet.vehicle.model/get_vehicle_model' \
--header 'Content-Type: application/json' \
--header 'Cookie: frontend_lang=en_US; session_id=8ace287e798f31740242c2a1cdbe8b45352d7e72' \
--data '{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "fleet.vehicle.model",
        "method": "get_vehicle_model",
        "args": [],
        "kwargs": {
            "offset": 0,
            "limit": 1
        }
    }
}'
```

Read Vehicle categories in your database
=============================

1. Use `get_vehicle_category` method on a database to read vehicle categories and their fields using domains:

    ```python
    @api.model
    def get_vehicle_category(self, offset=0, limit=None):
        """Get model category."""
        return self._get_vehicle_category(offset, limit)
    ```

## URL

```
POST /web/dataset/call_kw/fleet.vehicle.model.category/get_vehicle_category
```

Postman Demo: [session_read_vehicle_brand_model_category.json](postman_collection.json)

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

| Name        | Type    | Description                                                                   |
|-------------|---------|-------------------------------------------------------------------------------|
| `model`     | string  | The name of the Odoo model on which the method will be executed.              |
| `method`    | string  | The name of the public method to be called on the specified model.            |
| `args`      | list    | The positional arguments of the method, provided as a JSON-encoded list.      |
| `kwargs`    | dict    | The keyword arguments of the method, provided as a JSON-encoded object.     

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

## `args` structure

No input values are required into the args list.

## `kwargs` structure

| Name                   | Type    | Description                                                             |
|------------------------|---------|-------------------------------------------------------------------------|
| `offset`               | integer | The offset for the records to retrieve                                  |
| `limit`                | integer | Maximum limit of records to retrieve                                    |

## Response

The method returns a JSON object as a response:

| Name                 | Type    | Description                                                               |
|----------------------|---------|---------------------------------------------------------------------------|
|                      | list    | Vehicle categories                                                        |

## Request Example

```json
{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "fleet.vehicle.model.category",
        "method": "get_vehicle_category",
        "args": [],
        "kwargs": {
            "offset": 0,
            "limit": 4
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
            "id": 1,
            "name": "Break"
        },
        {
            "id": 2,
            "name": "SUV"
        },
        {
            "id": 3,
            "name": "Sport Car"
        },
        {
            "id": 4,
            "name": "Compact"
        }
    ]
}
```

## cURL Example

```bash
curl --location 'http://localhost:8069/web/dataset/call_kw/fleet.vehicle.model.category/get_vehicle_category' \
--header 'Content-Type: application/json' \
--header 'Cookie: frontend_lang=en_US; session_id=8ace287e798f31740242c2a1cdbe8b45352d7e72' \
--data '{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "model": "fleet.vehicle.model.category",
        "method": "get_vehicle_category",
        "args": [],
        "kwargs": {
            "offset": 0,
            "limit": 4
        }
    }
}'
```
