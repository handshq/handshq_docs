# Projects

## Creating a project

```shell
curl http://api.handshq.com/v1/projects \
  -H "Content-Type: application/json" \
  -H "Authorization: bearer [api_token]" \
  --request POST \
  -d "[json_payload]"
```

> Example Project creation payload.

```json
  {
    "project": {
      "name": "My project with extra details",
      "fields_attributes": {
        "1": "My field value"
      }
    }
  }

```

This endpoint allows you to create a project for the company who is registered with the api key you provide.

### HTTP Request

`POST https://api.handshq.com/v1/projects`

### Allowed Project Parameters

Parameter | Format | Required | Description
--------- | ------ | -------- | -----------
name | String | Yes | Name of your project, used for document titles, names of PDF documents etc.
start_date | DateTime | No | To denote when your project starts, used in conjunciton with `end_time` to denote whether project is still active.
end_date | DateTime | No | To denote when your project starts, used in conjunciton with `start_time` to denote whether project is still active.
reference | String | No | Your internal reference for a project e.g. 'RA01'

In order to set further details we need a reference to which fields you would like to set a value for, these can differ for each company if you are using custom templates. You can find these references with our `fields` endpoint.

<aside class="notice">
Remember — ensure that values for that field correspond to their <code>data_type</code>
</aside>

### Response

Successful requests will return a json payload of the project that was created and a `201` status code.

> 201

```json
{
  "data": {
      "id": "1",
      "type": "project",
      "attributes": {
          "name": "My Project"
      },
      "relationships": {
          "user": {
              "data": null
          }
      },
      "links": {
          "app_url": "https://app.handshq.com/projects/1"
      }
  }
}
```
