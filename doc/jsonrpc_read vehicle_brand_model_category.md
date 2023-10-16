Read Vehicle info using Jsonrpc
=============================

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

1. Use `search_read` method on a database to read vehicle brands and their fields using domains:

    ```python
    @api.model
    def search_read(self, domain=None, fields=None, offset=0, limit=None, order=None, **read_kwargs):
        """Perform a :meth:`search` followed by a :meth:`read`.

        :param domain: Search domain, see ``args`` parameter in :meth:`search`.
            Defaults to an empty domain that will match all records.
        :param fields: List of fields to read, see ``fields`` parameter in :meth:`read`.
            Defaults to all fields.
        :param int offset: Number of records to skip, see ``offset`` parameter in :meth:`search`.
            Defaults to 0.
        :param int limit: Maximum number of records to return, see ``limit`` parameter in :meth:`search`.
            Defaults to no limit.
        :param order: Columns to sort result, see ``order`` parameter in :meth:`search`.
            Defaults to no sort.
        :param read_kwargs: All read keywords arguments used to call
            ``read(..., **read_kwargs)`` method e.g. you can use
            ``search_read(..., load='')`` in order to avoid computing name_get
        :return: List of dictionaries containing the asked fields.
        :rtype: list(dict).
        """
        records = self.search(domain or [], offset=offset, limit=limit, order=order)
        if not records:
            return []

        if fields and fields == ['id']:
            # shortcut read if we only want the ids
            return [{'id': record.id} for record in records]

        # read() ignores active_test, but it would forward it to any downstream search call
        # (e.g. for x2m or function fields), and this is not the desired behavior, the flag
        # was presumably only meant for the main search().
        # TODO: Move this to read() directly?
        if 'active_test' in self._context:
            context = dict(self._context)
            del context['active_test']
            records = records.with_context(context)

        result = records.read(fields, **read_kwargs)
        if len(result) <= 1:
            return result

        # reorder read
        index = {vals['id']: vals for vals in result}
        return [index[record.id] for record in records if record.id in index]
    ```

## URL

```
POST /jsonrpc
```

Postman Demo: [jsonrpc_read_vehicle_brand.json](postman_collection.json)

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

## Input Parameters for the `[5]-args` list

The method expects to receive a JSON object in the request body with the following parameters:

## `args` structure

| Name                   | Type    | Description                                                             |
|------------------------|---------|-------------------------------------------------------------------------|
| `[0] - domain`            | list    | Domain with conditions applied to records of the model (if the domain is an empty list, it will retrieve all the records)  |

## `kwargs` structure

| Name                   | Type    | Description                                                            |
|------------------------|---------|------------------------------------------------------------------------|
| `fields`               | list    | Vehicle fields to read                                                 |

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
            "search_read",
            [
                [["name", "ilike", "au"]] // domain (looks for brands whose names contain "au")
            ],
            {
                "fields": ["name", "model_ids"],
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
            "search_read",
            [
                [["name", "ilike", "au"]]
            ],
            {
                "fields": ["name", "model_ids"],
                "context": {}
            }
        ]
    }
}'
```

Read Vehicle models in your database
--------------------------------

1. Use `read` method on a database to read a vehicle record and their fields:

    ```python
    def read(self, fields=None, load='_classic_read'):
        """ read([fields])

        Reads the requested fields for the records in ``self``, low-level/RPC
        method. In Python code, prefer :meth:`~.browse`.

        :param fields: list of field names to return (default is all fields)
        :return: a list of dictionaries mapping field names to their values,
                 with one dictionary per record
        :raise AccessError: if user has no read rights on some of the given
                records
        """
        fields = self.check_field_access_rights('read', fields)

        # fetch stored fields from the database to the cache
        stored_fields = set()
        for name in fields:
            field = self._fields.get(name)
            if not field:
                raise ValueError("Invalid field %r on model %r" % (name, self._name))
            if field.store:
                stored_fields.add(name)
            elif field.compute:
                # optimization: prefetch direct field dependencies
                for dotname in self.pool.field_depends[field]:
                    f = self._fields[dotname.split('.')[0]]
                    if f.prefetch and (not f.groups or self.user_has_groups(f.groups)):
                        stored_fields.add(f.name)
        self._read(stored_fields)

        return self._read_format(fnames=fields, load=load)
    ```

## URL

```
POST /jsonrpc
```

Postman Demo: [jsonrpc_read_vehicle_model.json](postman_collection.json)

## Input Parameters for the `[5]-args` list

The method expects to receive a JSON object in the request body with the following parameters:

## `args` structure

| Name                   | Type    | Description                                                             |
|------------------------|---------|-------------------------------------------------------------------------|
| `[0] - ids`            | list    | Record ids to read                                                      |

## `kwargs` structure

| Name                   | Type    | Description                                                             |
|------------------------|---------|-------------------------------------------------------------------------|
| `fields`               | list    | Vehicle fields to read                                                  |
| `load`                 | string  | `_classic_read`: display name will be computed using name_get           |

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
            "read", // public method
            [ // args
                [
                    13 // Vehicle model ID
                ]
            ],
            { // kwargs
                "fields": [ // fields to return
                    "id", // Vehicle model ID
                    "name", // Vehicle model name
                    "brand_id" // Vehicle brand
                ],
                "load": "_classic_read",
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
            "read", 
            [ 
                [
                    13
                ]
            ],
            { 
                "fields": [ 
                    "id", 
                    "name", 
                    "brand_id" 
                ],
                "load": "_classic_read",
                "context": {}
            }
        ]
    }
}'
```

Read Vehicle categories in your database
=============================

1. Use `search_read` method on a database to read vehicle categories and their fields using domains:

    ```python
    @api.model
    def search_read(self, domain=None, fields=None, offset=0, limit=None, order=None, **read_kwargs):
        """Perform a :meth:`search` followed by a :meth:`read`.

        :param domain: Search domain, see ``args`` parameter in :meth:`search`.
            Defaults to an empty domain that will match all records.
        :param fields: List of fields to read, see ``fields`` parameter in :meth:`read`.
            Defaults to all fields.
        :param int offset: Number of records to skip, see ``offset`` parameter in :meth:`search`.
            Defaults to 0.
        :param int limit: Maximum number of records to return, see ``limit`` parameter in :meth:`search`.
            Defaults to no limit.
        :param order: Columns to sort result, see ``order`` parameter in :meth:`search`.
            Defaults to no sort.
        :param read_kwargs: All read keywords arguments used to call
            ``read(..., **read_kwargs)`` method e.g. you can use
            ``search_read(..., load='')`` in order to avoid computing name_get
        :return: List of dictionaries containing the asked fields.
        :rtype: list(dict).
        """
        records = self.search(domain or [], offset=offset, limit=limit, order=order)
        if not records:
            return []

        if fields and fields == ['id']:
            # shortcut read if we only want the ids
            return [{'id': record.id} for record in records]

        # read() ignores active_test, but it would forward it to any downstream search call
        # (e.g. for x2m or function fields), and this is not the desired behavior, the flag
        # was presumably only meant for the main search().
        # TODO: Move this to read() directly?
        if 'active_test' in self._context:
            context = dict(self._context)
            del context['active_test']
            records = records.with_context(context)

        result = records.read(fields, **read_kwargs)
        if len(result) <= 1:
            return result

        # reorder read
        index = {vals['id']: vals for vals in result}
        return [index[record.id] for record in records if record.id in index]
    ```

## URL

```
POST /jsonrpc
```

Postman Demo: [jsonrpc_read_vehicle_category.json](postman_collection.json)

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

## Input Parameters for the `[5]-args` list

The method expects to receive a JSON object in the request body with the following parameters:

## `args` structure

| Name                   | Type    | Description                                                             |
|------------------------|---------|-------------------------------------------------------------------------|
| `[0] - domain`            | list    | Domain with conditions applied to records of the model (if the domain is an empty list, it will retrieve all the records) |

## `kwargs` structure

| Name                   | Type    | Description                                                             |
|------------------------|---------|-------------------------------------------------------------------------|
| `fields`               | list    | Vehicle fields to read                                                  |

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
            "search_read",
            [
                []
            ],
            {
                "fields": [
                    "name"
                ],
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
            "search_read",
            [
                []
            ],
            {
                "fields": ["name"],
                "context": {}
            }
        ]
    }
}'
```
