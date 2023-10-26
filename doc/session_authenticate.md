Authenticate With Session
=========================

This controller allows to authenticate the user by passing the database name, the username and password. It returns in the result an integer, which is the user ID authenticated.

Login to your database
---

1. Use `authenticate` method on a database to authenticate a user in Odoo and create a new session:

    ```python
    @http.route('/web/session/authenticate', type='json', auth="none")
    def authenticate(self, db, login, password, base_location=None):
        request.session.authenticate(db, login, password)
        return request.env['ir.http'].session_info()
    ```

## URL

```
POST /web/session/authenticate
```

Postman Demo: [session_authenticate.json](postman_collection.json)

## Input Parameters

The method expects to receive a JSON object in the request body with the following parameters:

| Name        | Type    | Description                                             |
|-------------|---------|---------------------------------------------------------|
| `db`        | string  | Odoo database name.                                     |
| `login`     | string  | Username or email address.                             |
| `password`  | string  | User's password.                                        |
| `base`      | string  | Odoo base URL. Defaults to `false`.                     |

## Response

The method returns a JSON object as a response:

| Name                  | Type    | Description                                                                           |
|-----------------------|---------|---------------------------------------------------------------------------------------|
| `uid`                 | integer | ID of the authenticated user in Odoo.                                                 |
| `is_system`           | bool    | Admin user with global setting access. (group_system)                                  |
| `is_admin`            | bool    | The user has administrator privileges. (SUPERUSER_ID or group_erp_manager)            |
| `user_context`        | dict    | User preferences. (lang, tz)                                                          |
| `db`                  | string  | DB name.                                                                              |
| `server_version`      | string  | Version.                                                                              |
| `server_version_info` | list    | Version in format: (MAJOR, MINOR, MICRO, RELEASE_LEVEL, SERIAL).                      |
| `support_url`         | string  | Url for help support                                                                  |
| `name`                | string  | Name of the authenticated user in Odoo.                                               |
| `username`            | string  | Username of the authenticated user in Odoo.                                           |
| `partner_display_name`| string  | Computed name displayed for a record in the user interface.                           |
| `company_id`          | integer | Default company of the authenticated user in Odoo.                                    |
| `partner_id`          | integer | Partner_id of the authenticated user in Odoo.                                         |
| `web.base.url`        | string  | Url base. (web.base.url param)                                                        |
| `active_ids_limit`    | integer | Maximum number of records that can be processed. (web.active_ids_limit param)         |
| `max_file_upload_size`| integer | Maximum allowed file size(bytes) for file uploads. (web.max_file_upload_size param)   |
| `currencies`          | dict    | Allowed Currencies                                      |
| `user_companies`      | dict    | 
Companies to which the current user belongs. Also shows the current user's company.         |

## Request Example

```json
{
    "params":{
        "db": "database_name",
        "login": "user_name",
        "password": "admin"
    }
}
```

## Response Example

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "uid": 2,
        "is_system": true,
        "is_admin": true,
        "user_context": {
            "lang": "en_US",
            "tz": "Europe/Brussels",
            "uid": 2
        },
        "db": "openerp_70104_confisa",
        "server_version": "16.0+e",
        "server_version_info": [
            16,
            0,
            0,
            "final",
            0,
            "e"
        ],
        "support_url": "https://www.odoo.com/help",
        "name": "Mitchell Admin",
        "username": "admin",
        "partner_display_name": "YourCompany, Mitchell Admin",
        "company_id": 1,
        "partner_id": 3,
        "web.base.url": "http://localhost:55001",
        "active_ids_limit": 20000,
        "max_file_upload_size": 134217728,
        "currencies": {
            "73": {
                "symbol": "RD$",
                "position": "before",
                "digits": [
                    69,
                    2
                ]
            },
            "1": {
                "symbol": "€",
                "position": "after",
                "digits": [
                    69,
                    2
                ]
            },
            "2": {
                "symbol": "$",
                "position": "before",
                "digits": [
                    69,
                    2
                ]
            }
        },
        "user_companies": {
            "current_company": 1,
            "allowed_companies": {
                "3": {
                    "id": 3,
                    "name": "DO Company",
                    "sequence": 10,
                    "timesheet_uom_id": 4,
                    "timesheet_uom_factor": 1.0
                },
                "2": {
                    "id": 2,
                    "name": "My Company (Chicago)",
                    "sequence": 10,
                    "timesheet_uom_id": 4,
                    "timesheet_uom_factor": 1.0
                },
                "1": {
                    "id": 1,
                    "name": "BanTracking GPS Dominicana",
                    "sequence": 0,
                    "timesheet_uom_id": 4,
                    "timesheet_uom_factor": 1.0
                }
            }
        }
    }
}
```

## cURL Example

```bash
curl --location 'http://localhost:8069/web/session/authenticate' \
--header 'Content-Type: application/json' \
--header 'Cookie: session_id=d2dc0193ea4a45bb94251506af9dd1314c00300a' \
--data '{
    "jsonrpc": "2.0",
    "id": 16,
    "params": {
        "db": "db_name",
        "login": "user_name",
        "password": "admin"
    }
}'
```
