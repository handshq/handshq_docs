# Teams

## Viewing your teams

If your account has the teams feature, this endpoint allows you to view the teams that belong to the division registered to the API token you provide.

```shell
curl https://api.handshq.com/v1/teams \
  -H "Accept: application/json" \
  -H "Authorization: bearer [api_token]"
```

> 200

```json
{
  "data": [
    {
      "id": "1",
      "type": "team",
      "attributes": {
        "name": "Team 1"
      }
    },
    {
      "id": "2",
      "type": "team",
      "attributes": {
        "name": "Team 2"
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

### Request

`GET https://api.handshq.com/v1/teams`

### Allowed Query Parameters

| Parameter | Format | Required | Description                                    |
| --------- | ------ | -------- | ---------------------------------------------- |
| search    | String | No       | Provide a search field to search the team name |

### Response

Successful requests will return a collection of teams and a `200` status code.
Results in `data` are [paginated](#pagination)
