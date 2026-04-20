# Folders

## Viewing your folders

If your account has the folders feature, this endpoint allows you to view the folders that belong to the division registered to the API token you provide.

```shell
curl https://api.handshq.com/v1/folders \
  -H "Accept: application/json" \
  -H "Authorization: bearer [api_token]"
```

> 200

```json
{
  "data": [
    {
      "id": "1",
      "type": "folder",
      "attributes": {
        "name": "Folder 1"
      }
    },
    {
      "id": "2",
      "type": "folder",
      "attributes": {
        "name": "Folder 2"
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

`GET https://api.handshq.com/v1/folders`

### Allowed Query Parameters

| Parameter | Format | Required | Description                                      |
| --------- | ------ | -------- | ------------------------------------------------ |
| search    | String | No       | Provide a search field to search the folder name |

### Response

Successful requests will return a collection of folders and a `200` status code.
Results in `data` are [paginated](#pagination)
