# Training

## Viewing your trainings

```shell
curl https://api.handshq.com/v1/trainings \
  -H "Accept: application/json" \
  -H "Authorization: bearer [api_token]"
```

> 200

```json
{
  "data": [
    {
      "id": "123",
      "type": "training",
      "attributes": {
        "start_date": "2026-01-01",
        "expiry_date": "2026-12-31",
        "notes": "Lorem ipsum..."
      },
      "relationships": {
        "course": {
          "data": {
            "id": "200",
            "type": "course"
          }
        },
        "personnel": {
          "data": {
            "id": "100",
            "type": "personnel"
          }
        }
      }
    },
    {
      "id": "234",
      "type": "training",
      "attributes": {
        "start_date": "2026-07-11",
        "expiry_date": "2027-07-10",
        "notes": null
      },
      "relationships": {
        "course": {
          "data": {
            "id": "300",
            "type": "course"
          }
        },
        "personnel": {
          "data": {
            "id": "400",
            "type": "personnel"
          }
        }
      }
    }
  ],
  "meta": {
    "pagination": {
      "requested_page": 1,
      "total_pages": 1
    }
  }
}
```

This endpoint allows you to view the trainings that belong to the personnel of the division registered to the API token you provide. If the division is the primary division for that account, all trainings across the account will be returned. If not, only the training for personnel of that division will be returned. The training index returns training for personnel that have not been archived.

### Request

`GET https://api.handshq.com/v1/trainings`

### Allowed Query Parameters

| Parameter    | Format | Required | Description                                                   |
| ------------ | ------ | -------- | ------------------------------------------------------------- |
| personnel_id | String | No       | Only trainings belonging to the personnel with this ID will be returned |

### Response

Successful requests will return a collection of trainings and a `200` status code, with the most recently created training first.
Results in `data` are [paginated](#pagination)

## Viewing one training

```shell
curl https://api.handshq.com/v1/trainings/[id] \
  -H "Accept: application/json" \
  -H "Authorization: bearer [api_token]"
```

> 200

```json
{
  "data": {
    "id": "123",
    "type": "training",
    "attributes": {
      "start_date": "2026-01-01",
      "expiry_date": "2026-12-31",
      "notes": "Lorem ipsum..."
    },
    "relationships": {
      "course": {
        "data": {
          "id": "200",
          "type": "course"
        }
      },
      "personnel": {
        "data": {
          "id": "100",
          "type": "personnel"
        }
      }
    }
  }
}
```

This endpoint allows you to view a training by providing the id. As with the index, only trainings belonging to the personnel of the division registered to the API token can be viewed, and a training belonging to another division or account will return a `404`.

### Request

`GET https://api.handshq.com/v1/trainings/[id]`

### Response

Successful requests will return a json payload of that training and a `200` status code

## Creating a training

```shell
curl https://api.handshq.com/v1/trainings \
  -H "Accept: application/json" \
  -H "Authorization: bearer [api_token]" \
  -H "Content-Type: application/json" \
  --request POST \
  -d '[json_payload]'
```

> Example Training creation payload.

```json
{
  "training": {
    "personnel_id": 2,
    "course_id": 3,
    "start_date": "2022-12-20",
    "expiry_date": "2024-12-20",
    "notes": "Training session notes"
  }
}
```

This endpoint allows you to add a training to a personnel using the ID of the personnel and the ID of the course.

### Request

`POST https://api.handshq.com/v1/trainings`

### Allowed Training Parameters

All parameters must be nested within `training`

| Parameter    | Format | Required                              | Description                                            |
| ------------ | ------ | ------------------------------------- | ------------------------------------------------------ |
| personnel_id | String | Yes                                   | The ID of the personnel                                |
| course_id    | String | Yes                                   | The ID of the course                                   |
| start_date   | String | Yes                                   | To denote when the training starts                     |
| expiry_date  | String | Yes (if the specified course expires) | To denote when the training expires if the course does |
| notes        | String | No                                    | Notes related to the training                          |

### Response

Successful requests will return a json payload of the newly created training and a `201` status code

> 201

```json
{
  "data": {
    "id": "1",
    "type": "training",
    "attributes": {
      "start_date": "2022-12-20",
      "expiry_date": "2024-12-20",
      "notes": "Training session notes"
    },
    "relationships": {
      "course": {
        "data": {
          "id": "3",
          "type": "course"
        }
      },
      "personnel": {
        "data": {
          "id": "2",
          "type": "personnel"
        }
      }
    }
  }
}
```
