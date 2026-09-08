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
with_role_training_statuses | Boolean | No | If set to true will include a training status breakdown per role and course in the `included` section of the response, as `personnel_role_requirement`, `personnel_role_course_requirement` and `personnel_unlinked_requirement` resources. See [Role training statuses](#role-training-statuses) for an example.

### Response

Successful requests will return a collection of personnel and a `200` status code.
Results in `data` are [paginated](#pagination)

### Role training statuses

By default, a personnel's `training_status` is a single aggregate across all of their training requirements. It tells you that someone is non-compliant, but not why - which of their roles is driving the requirement, which courses are assigned to that role, or whether a given course is required or optional.

Setting `with_role_training_statuses` to `true` adds that breakdown to the `included` section of the response:

Resource | Description
-------- | -----------
`personnel_role_requirement` | One per role held by the personnel, carrying the training status of that role
`personnel_role_course_requirement` | One per course assigned to one of those roles, carrying the training status of that course for that role
`personnel_unlinked_requirement` | One per course assigned to the personnel directly rather than through one of their roles

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
        "personnel_role_requirements": {
          "data": [
            {
              "id": "11dbd45e-a4bc-4890-8024-58db3d7241e1",
              "type": "personnel_role_requirement"
            }
          ]
        },
        "personnel_unlinked_requirements": {
          "data": [
            {
              "id": "38",
              "type": "personnel_unlinked_requirement"
            }
          ]
        }
      }
    }
  ],
  "included": [
    {
      "id": "11dbd45e-a4bc-4890-8024-58db3d7241e1",
      "type": "personnel_role_requirement",
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
        "personnel_role_course_requirements": {
          "data": [
            {
              "id": "01c69542-8b53-4394-b4d0-4ad82f345e62",
              "type": "personnel_role_course_requirement"
            }
          ]
        }
      }
    },
    {
      "id": "01c69542-8b53-4394-b4d0-4ad82f345e62",
      "type": "personnel_role_course_requirement",
      "attributes": {
        "required": true,
        "overridden": false,
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
      "id": "38",
      "type": "personnel_unlinked_requirement",
      "attributes": {
        "required": true,
        "training_status": {
          "status": "valid",
          "description": "training up-to-date"
        }
      },
      "relationships": {
        "personnel": {
          "data": {
            "id": "1234",
            "type": "personnel"
          }
        },
        "course": {
          "data": {
            "id": "222",
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
    },
    {
      "id": "222",
      "type": "course",
      "attributes": {
        "name": "First Aid"
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

The `personnel_role_requirement` for role `123` shows that John's Scaffolder role is `expired`, and the related `personnel_role_course_requirement` shows why: the required "Working at Height" course is expired. This is enough to render something like "Scaffolder - expired, because Working at Height is expired and required" instead of just "expired".

The `personnel_unlinked_requirement` covers "First Aid", a course assigned to John directly rather than through the Scaffolder role. Here it is `valid`, so it is not contributing to the `expired` status.

The `required` attribute tells you whether the course counts towards the training status of the requirement it belongs to. A requirement that needs no training has a `default` training status, described as "no training required".

#### Requirements that are not linked to a role

The examples below are abridged - only the relationships and included resources relevant to each case are shown.

```json
{
  "data": [
    {
      "id": "69",
      "type": "personnel",
      "relationships": {
        "roles": {
          "data": [
            {
              "id": "6",
              "type": "role"
            }
          ]
        },
        "personnel_role_requirements": {
          "data": [
            {
              "id": "d99d4e58-1a23-48d2-ac2b-aafd638daa69",
              "type": "personnel_role_requirement"
            }
          ]
        },
        "personnel_unlinked_requirements": {
          "data": [
            {
              "id": "38",
              "type": "personnel_unlinked_requirement"
            }
          ]
        }
      }
    }
  ],
  "included": [
    {
      "id": "d99d4e58-1a23-48d2-ac2b-aafd638daa69",
      "type": "personnel_role_requirement",
      "attributes": {
        "training_status": {
          "status": "missing",
          "description": "missing training"
        }
      },
      "relationships": {
        "role": {
          "data": {
            "id": "6",
            "type": "role"
          }
        },
        "personnel": {
          "data": {
            "id": "69",
            "type": "personnel"
          }
        },
        "personnel_role_course_requirements": {
          "data": [
            {
              "id": "e27fe11b-2209-48d0-8616-03d724155d03",
              "type": "personnel_role_course_requirement"
            }
          ]
        }
      }
    },
    {
      "id": "e27fe11b-2209-48d0-8616-03d724155d03",
      "type": "personnel_role_course_requirement",
      "attributes": {
        "required": false,
        "overridden": false,
        "training_status": {
          "status": "default",
          "description": "no training required"
        }
      },
      "relationships": {
        "personnel": {
          "data": {
            "id": "69",
            "type": "personnel"
          }
        },
        "role": {
          "data": {
            "id": "6",
            "type": "role"
          }
        },
        "course": {
          "data": {
            "id": "173",
            "type": "course"
          }
        }
      }
    },
    {
      "id": "38",
      "type": "personnel_unlinked_requirement",
      "attributes": {
        "required": true,
        "training_status": {
          "status": "missing",
          "description": "missing training"
        }
      },
      "relationships": {
        "personnel": {
          "data": {
            "id": "69",
            "type": "personnel"
          }
        },
        "course": {
          "data": {
            "id": "36",
            "type": "course"
          }
        }
      }
    },
    {
      "id": "173",
      "type": "course",
      "attributes": {
        "name": "Booking course"
      }
    },
    {
      "id": "36",
      "type": "course",
      "attributes": {
        "name": "Affenpinscher"
      }
    },
    {
      "id": "6",
      "type": "role",
      "attributes": {
        "position": "Generic Role 1"
      }
    }
  ]
}
```

A `personnel_unlinked_requirement` is a course assigned to the personnel individually, so it is not tied to any one role. It therefore counts towards the training status of *every* `personnel_role_requirement` the personnel has.

In this example the only course assigned through the "Generic Role 1" role is not required, so its `personnel_role_course_requirement` has a `default` status. The role requirement is still `missing`, because the unlinked "Affenpinscher" requirement is required and has no training against it. If the personnel held more roles, each of their role requirements would be `missing` for the same reason.

#### The same course required by one role and not another

```json
{
  "included": [
    {
      "id": "b5ddf0ba-0ba9-46b8-87d1-c46123d9a97a",
      "type": "personnel_role_requirement",
      "attributes": {
        "training_status": {
          "status": "missing",
          "description": "missing training"
        }
      },
      "relationships": {
        "role": {
          "data": {
            "id": "70",
            "type": "role"
          }
        },
        "personnel": {
          "data": {
            "id": "69",
            "type": "personnel"
          }
        },
        "personnel_role_course_requirements": {
          "data": [
            {
              "id": "0e2cd1ff-0912-4247-b331-8a0c75e3c6f8",
              "type": "personnel_role_course_requirement"
            }
          ]
        }
      }
    },
    {
      "id": "9173a320-a763-46b4-8f44-68e83ec3dfdd",
      "type": "personnel_role_requirement",
      "attributes": {
        "training_status": {
          "status": "default",
          "description": "no training required"
        }
      },
      "relationships": {
        "role": {
          "data": {
            "id": "64",
            "type": "role"
          }
        },
        "personnel": {
          "data": {
            "id": "69",
            "type": "personnel"
          }
        },
        "personnel_role_course_requirements": {
          "data": [
            {
              "id": "33bc2057-ff6a-4039-8d86-b8aca587c728",
              "type": "personnel_role_course_requirement"
            }
          ]
        }
      }
    },
    {
      "id": "0e2cd1ff-0912-4247-b331-8a0c75e3c6f8",
      "type": "personnel_role_course_requirement",
      "attributes": {
        "required": true,
        "overridden": false,
        "training_status": {
          "status": "missing",
          "description": "missing training"
        }
      },
      "relationships": {
        "personnel": {
          "data": {
            "id": "69",
            "type": "personnel"
          }
        },
        "role": {
          "data": {
            "id": "70",
            "type": "role"
          }
        },
        "course": {
          "data": {
            "id": "171",
            "type": "course"
          }
        }
      }
    },
    {
      "id": "33bc2057-ff6a-4039-8d86-b8aca587c728",
      "type": "personnel_role_course_requirement",
      "attributes": {
        "required": false,
        "overridden": false,
        "training_status": {
          "status": "default",
          "description": "no training required"
        }
      },
      "relationships": {
        "personnel": {
          "data": {
            "id": "69",
            "type": "personnel"
          }
        },
        "role": {
          "data": {
            "id": "64",
            "type": "role"
          }
        },
        "course": {
          "data": {
            "id": "171",
            "type": "course"
          }
        }
      }
    },
    {
      "id": "171",
      "type": "course",
      "attributes": {
        "name": "Course 1"
      }
    },
    {
      "id": "70",
      "type": "role",
      "attributes": {
        "position": "required role"
      }
    },
    {
      "id": "64",
      "type": "role",
      "attributes": {
        "position": "Optional Role"
      }
    }
  ]
}
```

The same course can be assigned to more than one of a personnel's roles, and be required by one of them but not the other. You get one `personnel_role_course_requirement` per role and course pair, so each role reports the status the course has *for that role*.

Here "Course 1" is assigned to both roles and the personnel has no training against it. It is required by the "required role" role, so that role requirement is `missing`. It is not required by the "Optional Role" role, so that role requirement is `default`.

#### A course that overrides its role

```json
{
  "included": [
    {
      "id": "9886a3ba-a5bd-42cf-8455-cebf87bdd493",
      "type": "personnel_role_requirement",
      "attributes": {
        "training_status": {
          "status": "default",
          "description": "no training required"
        }
      },
      "relationships": {
        "role": {
          "data": {
            "id": "70",
            "type": "role"
          }
        },
        "personnel": {
          "data": {
            "id": "69",
            "type": "personnel"
          }
        },
        "personnel_role_course_requirements": {
          "data": [
            {
              "id": "6d2360f5-fc6e-4f35-bf99-265a32fc8c58",
              "type": "personnel_role_course_requirement"
            }
          ]
        }
      }
    },
    {
      "id": "6d2360f5-fc6e-4f35-bf99-265a32fc8c58",
      "type": "personnel_role_course_requirement",
      "attributes": {
        "required": false,
        "overridden": true,
        "training_status": {
          "status": "default",
          "description": "no training required"
        }
      },
      "relationships": {
        "personnel": {
          "data": {
            "id": "69",
            "type": "personnel"
          }
        },
        "role": {
          "data": {
            "id": "70",
            "type": "role"
          }
        },
        "course": {
          "data": {
            "id": "171",
            "type": "course"
          }
        }
      }
    },
    {
      "id": "171",
      "type": "course",
      "attributes": {
        "name": "Course 1"
      }
    },
    {
      "id": "70",
      "type": "role",
      "attributes": {
        "position": "required role"
      }
    }
  ]
}
```

`overridden` tells you that the `required` value on a `personnel_role_course_requirement` was set against this personnel and course, rather than inherited from the role.

In this example the "required role" role requires "Course 1", but the requirement has been overridden for this personnel, so `required` is `false` and `overridden` is `true`. The course is not counted, and the role requirement is `default` even though the role itself requires the course.

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
