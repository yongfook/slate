---
title: Bannerbear API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - <a href='https://app.bannerbear.com'>Sign up for an API key</a>
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the Bannerbear API! 

Bannerbear is a service that exposes design templates as a simple JSON-based API. 

1. Your designer designs a template in Bannerbear
2. We turn it into an API
3. You use this API to generate variations of the template in JPG format

![](/images/api_example_2.jpg)

# Authentication

> To authorize:

```shell
curl "https://api.bannerbear/v2/requests"
  -H "Authorization: API_KEY"
```

> Make sure to replace `API_KEY` with your API key.

Bannerbear uses API keys to allow access to the API. You can get an API key by [creating a project](https://app.bannerbear.com) in Bannerbear.

Bannerbear expects the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: API_KEY`

<aside class="success">
Replace <code>API_KEY</code> with your project API key.
</aside>

# Requests

## Create a Request

> To create a request:

```shell
curl "https://api.bannerbear/v2/requests"
  -H "Authorization: API_KEY"
  -H "Content-Type: application/json" 
  -d json
```

> The above endpoint returns JSON like this:

```json
{
  "created_at": "2020-02-20T02:58:37.889Z",
  "status": "pending",
  "uid": "kG39R5XbvPQpLENKBWJj",
  "modifications": [
    {
      "name": "title",
      "text": "Lorem ipsum dolor sit amed"
    },
    {
      "name": "avatar",
      "image": "https://www.bannerbear.com/images/sample.jpg"
    }
  ],
  "image_url": "",
  "self": "https://api.bannerbear/v2/requests/kG39R5XbvPQpLENKBWJj"
}
```

This endpoint creates a new image request.

### HTTP Request

`POST https://api.bannerbear/v2/requests`

### Post Parameters

Parameter | Type | Description
--------- | ------- | -----------
template | string | The template id you want to use
modifications | array | A list of modifications you want to make


### Modification Object

Attribute | Type | Description
--------- | ------- | -----------
name | string | The name / label of the item you want to change
text | string | Replacement text you want to use
image | string | Replacement image url you want to use (must be publicly viewable)

All requests are created with the status `pending`.

Images are usually rendered within a few seconds. You can poll the GET endpoint for status updates. The `self` attribute of the response provides this endpoint.

## Get a Specific Request

> To get a request:

```shell
curl "https://api.bannerbear/v2/requests/kG39R5XbvPQpLENKBWJj"
  -H "Authorization: API_KEY"
```

> The above endpoint returns JSON like this:

```json
{
  "created_at": "2020-02-20T02:58:37.889Z",
  "status": "completed",
  "uid": "kG39R5XbvPQpLENKBWJj",
  "modifications": [
    {
      "name": "title",
      "text": "Lorem ipsum dolor sit amed"
    },
    {
      "name": "avatar",
      "image": "https://www.bannerbear.com/images/sample.jpg"
    }
  ],
  "image_url": "url_to_rendered_image",
  "self": "https://api.bannerbear/v2/requests/kG39R5XbvPQpLENKBWJj"
}
```

This endpoint retrieves a specific request.

After creating an image request you will receive a uid. You can poll this endpoint to find out when the image has been rendered. When the status is `completed` the image url will be available in `image_url`.

### HTTP Request

`GET https://api.bannerbear/v2/requests/<uid>`

### Parameters

Parameter | Description
--------- | -----------
uid | The uid of the request to retrieve
