# Personnel

## Viewing your personnel

```shell
curl https://api.handshq.com/v1/personnel \
  -H "Accept: application/json" \
  -H "Authorization: bearer [api_token]"
```

> 200

```json
{
  "data": [
    {
      "id": "1234",
      "type": "personnel",
      "attributes": {
        "first_name": "John",
        "last_name": "Smith",
        "email": "john.smith@email.com",
        "archived_at": null,
        "external_id": "JSMITH",
        "type": "employee",
        "training_status": {
          "status": "missing",
          "description": "missing training"
        }
      },
      "relationships": {
        "line_manager": {
          "data": {
            "id": "4321",
            "type": "line_manager"
          }
        },
        "roles": {
          "data": [
            {
              "id": "123",
              "type": "role"
            },
            {
              "id": "321",
              "type": "role"
            }
          ]
        },
        "teams": {
          "data": [
            {
              "id": "123",
              "type": "team"
            },
            {
              "id": "321",
              "type": "team"
            }
          ]
        },
        "primary_role": {
          "data": {
            "id": "123",
            "type": "primary_role"
          }
        }
      },
    }
  ],
  "meta": {
    "pagination": {
      "requested_page": 1,
      "total_pages": 1
    },
    "is_archived_set": false
  }
}
```

This endpoint allows you to view the personnel that belong to the division registered to the API token you provide. If the division is the primary division for that account, all personnel across the account will be returned. If not, only the personnel of that division will be returned. By default the personnel index returns personnel that have not been archived.

### Request

`GET https://api.handshq.com/v1/personnel`

### Allowed Query Parameters
Parameter | Format | Required | Description
--------- | ------ | -------- | -----------
search    | String | No       | Provide a search field to search the first name, last name or email address of personnel
archived  | String | No       | Provide a param of true to fetch only archived personnel
with_role_training_statuses | Boolean | No | If set to true will include a training status breakdown per role and course in the `included` section of the response, as `personnel_role_requirement_report` and `personnel_role_course_requirement_report` resources. See [Role training statuses](#role-training-statuses) for an example.

### Response

Successful requests will return a collection of personnel and a `200` status code.
Results in `data` are [paginated](#pagination)

### Role training statuses

By default, a personnel's `training_status` is a single aggregate across all of their assigned roles. It tells you that someone is non-compliant, but not why - which of their roles is driving the requirement, which courses are assigned to that role, or whether a given course is required or optional.

Setting `with_role_training_statuses` to `true` adds that breakdown to the `included` section of the response: one `personnel_role_requirement_report` per role held by the personnel, and one `personnel_role_course_requirement_report` per course assigned to that role.

```shell
curl https://api.handshq.com/v1/personnel?with_role_training_statuses=true \
  -H "Accept: application/json" \
  -H "Authorization: bearer [api_token]"
```

> 200

```json
{
  "data": [
    {
      "id": "1234",
      "type": "personnel",
      "attributes": {
        "first_name": "John",
        "last_name": "Smith",
        "email": "john.smith@email.com",
        "archived_at": null,
        "external_id": "JSMITH",
        "type": "employee",
        "training_status": {
          "status": "expired",
          "description": "expired training"
        }
      },
      "relationships": {
        "line_manager": {
          "data": {
            "id": "4321",
            "type": "line_manager"
          }
        },
        "roles": {
          "data": [
            {
              "id": "123",
              "type": "role"
            }
          ]
        },
        "teams": {
          "data": []
        },
        "primary_role": {
          "data": {
            "id": "123",
            "type": "primary_role"
          }
        },
        "personnel_role_requirement_reports": {
          "data": [
            {
              "id": "11dbd45e-a4bc-4890-8024-58db3d7241e1",
              "type": "personnel_role_requirement_report"
            }
          ]
        }
      }
    }
  ],
  "included": [
    {
      "id": "11dbd45e-a4bc-4890-8024-58db3d7241e1",
      "type": "personnel_role_requirement_report",
      "attributes": {
        "training_status": {
          "status": "expired",
          "description": "expired training"
        }
      },
      "relationships": {
        "role": {
          "data": {
            "id": "123",
            "type": "role"
          }
        },
        "personnel": {
          "data": {
            "id": "1234",
            "type": "personnel"
          }
        },
        "personnel_role_course_requirement_reports": {
          "data": [
            {
              "id": "01c69542-8b53-4394-b4d0-4ad82f345e62",
              "type": "personnel_role_course_requirement_report"
            }
          ]
        }
      }
    },
    {
      "id": "01c69542-8b53-4394-b4d0-4ad82f345e62",
      "type": "personnel_role_course_requirement_report",
      "attributes": {
        "course_id": 111,
        "required": true,
        "training_status": {
          "status": "expired",
          "description": "expired training"
        }
      },
      "relationships": {
        "personnel": {
          "data": {
            "id": "1234",
            "type": "personnel"
          }
        },
        "role": {
          "data": {
            "id": "123",
            "type": "role"
          }
        },
        "course": {
          "data": {
            "id": "111",
            "type": "course"
          }
        }
      }
    },
    {
      "id": "123",
      "type": "role",
      "attributes": {
        "position": "Scaffolder"
      }
    },
    {
      "id": "111",
      "type": "course",
      "attributes": {
        "name": "Working at Height"
      }
    }
  ],
  "meta": {
    "pagination": {
      "requested_page": 1,
      "total_pages": 1
    },
    "is_archived_set": false
  }
}
```

The `personnel_role_requirement_report` for role `123` shows that John's Scaffolder role is `expired`, and the related `personnel_role_course_requirement_report` shows why: the required "Working at Height" course is expired. This is enough to render something like "Scaffolder - expired, because Working at Height is expired and required" instead of just "expired".

`with_role_training_statuses` is also supported on [viewing one personnel](#viewing-one-personnel).

## Viewing one personnel

```shell
curl https://api.handshq.com/v1/personnel/[id] \
  -H "Accept: application/json" \
  -H "Authorization: bearer [api_token]"
```

> 200

```json
  {
    "data": {
      "id": "1234",
      "type": "personnel",
      "attributes": {
        "first_name": "John",
        "last_name": "Smith",
        "email": "john.smith@email.com",
        "archived_at": null,
        "external_id": "JSMITH",
        "type": "employee",
        "training_status": {
          "status": "missing",
          "description": "missing training"
      },
      "relationships": {
        "line_manager": {
          "data": {
            "id": "4321",
            "type": "line_manager"
          }
        },
        "roles": {
          "data": [
            {
              "id": "123",
              "type": "role"
            },
            {
              "id": "321",
              "type": "role"
            }
          ]
        },
        "teams": {
          "data": [
            {
              "id": "123",
              "type": "team"
            },
            {
              "id": "321",
              "type": "team"
            }
          ]
        },
        "primary_role": {
          "data": {
            "id": "123",
            "type": "primary_role"
          }
        }
      }
    }
  }
```

This endpoint allows you to view a personnel by providing the id.

### Request

`GET https://api.handshq.com/v1/personnel/[id]`

### Allowed Query Parameters
Parameter | Format | Required | Description
--------- | ------ | -------- | -----------
with_role_training_statuses | Boolean | No | If set to true will include a training status breakdown per role and course in the `included` section of the response. See [Role training statuses](#role-training-statuses) for an example.

### Response

Successful requests will return a json payload of that personnel and a `200` status code

## Creating a personnel

```shell
curl https://api.handshq.com/v1/personnel \
  -H "Accept: application/json" \
  -H "Authorization: bearer [api_token]" \
  -H "Content-Type: application/json" \
  --request POST \
  -d '[json_payload]'
```

> Example Personnel creation payload.

```json
  {
    "personnel": {
      "first_name": "Sandra",
      "last_name": "Smith",
      "email":"sandra.smith@email.com",
      "external_id": "JSMITH",
      "line_manager_id": "456",
      "role_ids": ["987", "765"],
      "team_ids": ["987", "765"],
      "primary_role_id": "987"
    }
  }
```

This endpoint allows you to create a personnel. Personnel are division specific, so the personnel will belong to the division that the API key belongs to. You can set the first name, last name and email of the personnel through this endpoint. You can also assign the personnel a line manager using the ID of another personnel, and assign roles to the personnel using the IDs of roles that exist in the account.

### Request

`POST https://api.handshq.com/v1/personnel`

### Allowed Personnel Parameters for create
All parameters must be nested within `personnel`

Parameter | Format | Required | Description
--------- | ------ | -------- | -----------
first_name | String | Yes | First name of the personnel
last_name | String | Yes | Last name of the personnel
email | String | No | Email address of the personnel
profile_access_enabled | Boolean | No | Allow employee to view their profile (permitted if personnel profile access feature enabled)
external_id | String | No | External ID of the personnel
line_manager_id | String | No | ID of the line manager of the personnel
role_ids | Association IDs | No | IDs of roles that exist in the training register, that the personnel holds. For more information see [Associations](#associations)
team_ids | Association IDs | No | IDs of teams that exist in the training register, that the personnel is a member of. For more information see [Associations](#associations)
primary_role_id | String | No | ID of the primary role of the personnel

### Response

Successful requests will return a json payload of the newly created personnel and a `201` status code

> 201

```json
  {
    "data": {
      "id": "123",
      "type": "personnel",
      "attributes": {
        "first_name": "Sandra",
        "last_name": "Smith",
        "email": "sandra.smith@email.com",
        "external_id": "JSMITH",
        "archived_at": null,
        "type": "employee"
      },
      "relationships": {
        "line_manager": {
          "data": {
            "id": "456",
            "type": "line_manager"
          }
        },
        "roles": {
          "data": [
            {
              "id": "987",
              "type": "role"
            },
            {
              "id": "765",
              "type": "role"
            }
          ]
        },
        "teams": {
          "data": [
            {
              "id": "987",
              "type": "team"
            },
            {
              "id": "765",
              "type": "team"
            }
          ]
        },
        "primary_role": {
          "data": {
            "id": "987",
            "type": "primary_role"
          }
        }
      }
    }
  }
```

## Updating a personnel

```shell
curl https://api.handshq.com/v1/personnel/[id] \
  -H "Accept: application/json" \
  -H "Authorization: bearer [api_token]" \
  -H "Content-Type: application/json" \
  --requst PATCH
  -d '[json_payload]'
```

> Example Personnel update payload.

```json
  {
    "personnel": {
      "first_name": "Sally",
      "last_name": "Smith-West",
      "email":"sally-sw@email.com",
      "external_id": "JSMITH",
      "line_manager_id": "567",
      "role_ids": ["345"],
      "team_ids": ["345"],
      "primary_role_id": "345"
    }
  }
```

This endpoint allows you to update the first name, last name, email address, line manager and roles of an existing personnel.

### Request

`PATCH https://api.handshq.com/v1/personnel/[id]`


### Allowed Personnel Parameters for update
All parameters must be nested within `personnel`

Parameter | Format | Required | Description
--------- | ------ | -------- | -----------
first_name | String | No | First name of the personnel
last_name | String | No | Last name of the personnel
email | String | No | Email address of the personnel
profile_access_enabled | Boolean | No | Allow employee to view their profile (permitted if personnel profile access feature enabled)
external_id | String | No | External ID of the personnel
line_manager_id | String | No | ID of the line manager of the personnel
role_ids | Association IDs | No | IDs of roles that exist in the training register, that the personnel holds. For more information see [Associations](#associations)
team_ids | Association IDs | No | IDs of teams that exist in the training register, that the personnel is a member of. For more information see [Associations](#associations)
primary_role_id | String | No | ID of the primary role of the personnel

### Response

Successful requests will return a json payload of the updated personnel and a `200` status code

> 200

```json
  {
    "data": {
      "id": "123",
      "type": "personnel",
      "attributes": {
        "first_name": "Sally",
        "last_name": "Smith-West",
        "email": "sally-sw@email.com",
        "external_id": "JSMITH",
        "archived_at": null,
        "type": "employee"
      },
      "relationships": {
        "line_manager": {
          "data": {
            "id": "567",
            "type": "line_manager"
          }
        },
        "roles": {
          "data": [
            {
              "id": "345",
              "type": "role"
            }
          ]
        },
        "teams": {
          "data": [
            {
              "id": "345",
              "type": "team"
            }
          ]
        },
        "primary_role": {
          "data": {
            "id": "345",
            "type": "primary_role"
          }
        }
      }
    }
  }

```

## Deleting a personnel

```shell
curl https://api.handshq.com/v1/personnel/[id] \
  -H "Accept: application/json" \
  -H "Authorization: bearer [api_token]"
  --request DELETE
```

> 204

This endpoint allows you to delete a personnel.

### Request

`DELETE https://api.handshq.com/v1/personnel/[id]`

### Response

Successful requests will return a `204` status code

## Archiving a personnel

```shell
curl https://api.handshq.com/v1/personnel/[id]/archive \
  -H "Accept: application/json" \
  -H "Authorization: bearer [api_token]"
  --request PATCH
```

> 200

This endpoint allows you to archive a personnel.

### Request

`PATCH https://api.handshq.com/v1/personnel/[id]/archive`

### Response

Successful requests will return a json payload of the archived personnel and a `200` status code

> 200

```json
   {
    "data": {
      "id": "123",
      "type": "personnel",
      "attributes": {
        "first_name": "Sally",
        "last_name": "Smith-West",
        "email": "sally-sw@email.com",
        "external_id": "JSMITH",
        "archived_at": "2022-04-27T17:30:18.835+01:00",
        "type": "employee"
      },
      "relationships": {
        "line_manager": {
          "data": {
            "id": "567",
            "type": "line_manager"
          }
        },
        "roles": {
          "data": [
            {
              "id": "345",
              "type": "role"
            }
          ]
        },
        "teams": {
          "data": [
            {
              "id": "345",
              "type": "team"
            }
          ]
        },
        "primary_role": {
          "data": {
            "id": "345",
            "type": "primary_role"
          }
        }
      }
    }
  }
```


## Unarchiving a personnel

```shell
curl https://api.handshq.com/v1/personnel/[id]/unarchive \
  -H "Accept: application/json" \
  -H "Authorization: bearer [api_token]"
  --request PATCH
```

> 200

This endpoint allows you to unarchive an archived personnel.

> 200

```json
   {
    "data": {
      "id": "123",
      "type": "personnel",
      "attributes": {
        "first_name": "Sally",
        "last_name": "Smith-West",
        "email": "sally-sw@email.com",
        "external_id": "JSMITH",
        "archived_at": null,
        "type": "employee"
      },
      "relationships": {
        "line_manager": {
          "data": {
            "id": "567",
            "type": "line_manager"
          }
        },
        "roles": {
          "data": [
            {
              "id": "345",
              "type": "role"
            }
          ]
        },
        "teams": {
          "data": [
            {
              "id": "345",
              "type": "team"
            }
          ]
        },
        "primary_role": {
          "data": {
            "id": "345",
            "type": "primary_role"
          }
        }
      }
    }
  }
```

### Request

`PATCH https://api.handshq.com/v1/personnel/[id]/unarchive`

### Response

Successful requests will return a json payload of the archived personnel and a `200` status code
