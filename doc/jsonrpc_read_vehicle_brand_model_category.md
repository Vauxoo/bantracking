Read Vehicle info using Jsonrpc
=============================

This endpoints allows to retrieve info to look for the vehicle brands, vehicle models and vehicle categories to be used to create or update a Sales order. 

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

Postman Demo: [jsonrpc_read_vehicle_brand_model_category.json](postman_collection.json)

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

| Name        | Type    | Description                                                                        |
|-------------|---------|------------------------------------------------------------------------------------|
| `service`   | string  | Type of service. (common, db, object)                                              |
| `method`    | string  | Name of the Odoo method to call.                                                   |
| `args`      | list    | The positional arguments of the method, provided as a JSON-encoded list.           |

## `args` structure

| Name                   | Type    | Description                                                             |
|------------------------|---------|-------------------------------------------------------------------------|
| `[0] - db_name`        | string  | Odoo database name                                                      |
| `[1] - uid`            | integer | User ID                                                                 |
| `[2] - password`       | string  | Password or [Token](https://www.odoo.com/documentation/16.0/developer/reference/external_api.html#api-keys)|
| `[3] - model`          | string  | The name of the Odoo model on which the method will be executed.        |
| `[4] - method`         | string  | The name of the public method to be called on the specified Odoo model. |
| `[5] - args`           | list    | The positional arguments of the method, provided as a JSON-encoded list.|
| `[6] - kwargs`         | dict    | The keyword arguments of the method, provided as a JSON-encoded object. |

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
POST /jsonrpc
```

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
        "service": "object",
        "method": "execute_kw",
        "args": [
            "confisa", // database
            2, // uid
            "admin",
            "fleet.vehicle.model.brand",
            "get_vehicle_model_brand",
            [],
            {
                "offset": 0,
                "limit": 3
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
curl --location --request GET 'http://localhost:8069/jsonrpc' \
--header 'Content-Type: application/json' \
--data-raw '{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "service": "object",
        "method": "execute_kw",
        "args": [
            "confisa", 
            2, 
            "admin",
            "fleet.vehicle.model.brand",
            "get_vehicle_model_brand",
            [],
            {
                "offset": 0,
                "limit": 3
            }
        ]
    }
}'
```

Read Vehicle models in your database
--------------------------------

1. Use `get_vehicle_model` method on a database to read a vehicle record and their fields:

    ```python
    @api.model
    def get_vehicle_model(self, offset=0, limit=None):
        """Get Vehicle Model."""
        return self._get_vehicle_model(offset, limit)
    ```

## URL

```
POST /jsonrpc
```

Postman Demo: [jsonrpc_read_vehicle_brand_model_category.json](postman_collection.json)

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
        "service": "object",
        "method": "execute_kw",
        "args": [
            "confisa", // database
            2, // uid
            "admin", // password or token
            "fleet.vehicle.model", // model
            "get_vehicle_model", // public method
            [],
            { // kwargs
                "offset": 0,
                "limit": 1
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
curl --location 'http://localhost:8069/jsonrpc' \
--header 'Content-Type: application/json' \
--data '{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "service": "object",
        "method": "execute_kw",
        "args": [
            "confisa", 
            2, 
            "admin",
            "fleet.vehicle.model",
            "get_vehicle_model", 
            [],
            { 
                "offset": 0,
                "limit": 1
            }
        ]
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
POST /jsonrpc
```

Postman Demo: [jsonrpc_read_vehicle_brand_model_category.json](postman_collection.json)

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
        "service": "object",
        "method": "execute_kw",
        "args": [
            "confisa", // database
            2, // uid
            "admin",
            "fleet.vehicle.model.category",
            "get_vehicle_category",
            [],
            {
                "offset": 0,
                "limit": 4
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
curl --location --request GET 'http://localhost:8069/jsonrpc' \
--header 'Content-Type: application/json' \
--data-raw '{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "service": "object",
        "method": "execute_kw",
        "args": [
            "confisa", 
            2, 
            "admin",
            "fleet.vehicle.model.category",
            "get_vehicle_category",
            [],
            {
                "offset": 0,
                "limit": 4
            }
        ]
    }
}'
```
