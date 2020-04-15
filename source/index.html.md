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

> To test your API key:

```shell
curl "https://api.bannerbear.com/v2/auth"
  -H "Authorization: Bearer API_KEY"
```

> Make sure to replace `API_KEY` with your API key.

Bannerbear uses API keys to allow access to the API. You can get an API key by [creating a project](https://app.bannerbear.com) in Bannerbear.

Bannerbear expects the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: Bearer API_KEY`

<aside class="success">
Replace <code>API_KEY</code> with your project API key
</aside>

# Images

## Create an Image

> To create a request:

```shell
curl "https://api.bannerbear.com/v2/images"
  -H "Authorization: Bearer API_KEY"
  -H "Content-Type: application/json" 
  -d json
```

> The above endpoint returns JSON like this:

```json
{
  "created_at": "2020-02-20T07:59:23.077Z",
  "status": "pending",
  "self": "https://api.bannerbear.com/v2/images/kG39R5XbvPQpLENKBWJj",
  "uid": "kG39R5XbvPQpLENKBWJj",
  "image_url": null,
  "template": "6A37YJe5qNDmpvWKP0",
  "modifications": [
    {
      "name": "title",
      "text": "Lorem ipsum dolor sit amed",
      "color": null,
      "background": null
    },
    {
      "name": "avatar",
      "image_url": "https://www.bannerbear.com/assets/sample_avatar.jpg"
    }
  ],
  "webhook_url": null,
  "webhook_response_code": null
}
```

This endpoint creates a new image.

### HTTP Request

`POST https://api.bannerbear.com/v2/images`

### Post Parameters

Parameter | Type | Description
--------- | ------- | -----------
template | string | The template id you want to use
modifications | array | A list of Modifications you want to make
webhook_url<br /><span class="tag">optional</span> | string | A url to POST the Image object to upon rendering completed

### Modification Object

Attribute | Type | Description
--------- | ------- | -----------
name | string | The name of the item you want to change
text | string | Replacement text you want to use
color | string | Color hex of object e.g. "#FF0000"
background | string | Color hex of text background
image_url | string | Replacement image url you want to use (must be publicly viewable)

All attributes are optional, for example if you do not specify a color, the object's default color will be used. 

### Status

All images are created with the status `pending`.

Images are usually rendered within a few seconds. When completed, the status changes to `completed`.

You can poll the GET endpoint for status updates. The `self` attribute of the response provides this endpoint.

### Webhooks

Instead of polling, we recommended you define a webhook in `webhook_url` which Bannerbear will POST the Image object to after image rendering is complete. The rendered image url is found in the `image_url` attribute of the Image object.

To secure your webhook endpoint, Bannerbear will POST with the following header:

`Authorization: Bearer WEBHOOK_KEY`

<aside class="success">
Bannerbear will replace <code>WEBHOOK_KEY</code> with your project's webhook key
</aside>

You can find your project's webhook key in its settings page.

## Get a Specific Image

> To get an image:

```shell
curl "https://api.bannerbear.com/v2/images/kG39R5XbvPQpLENKBWJj"
  -H "Authorization: Bearer API_KEY"
```

> The above endpoint returns JSON like this:

```json
{
  "created_at": "2020-02-20T07:59:23.077Z",
  "status": "completed",
  "self": "https://api.bannerbear.com/v2/images/kG39R5XbvPQpLENKBWJj",
  "uid": "kG39R5XbvPQpLENKBWJj",
  "image_url": "https://cdn.bannerbear.com/...",
  "template": "6A37YJe5qNDmpvWKP0",
  "modifications": [
    {
      "name": "title",
      "text": "Lorem ipsum dolor sit amed",
      "color": null,
      "background": null
    },
    {
      "name": "avatar",
      "image_url": "https://www.bannerbear.com/assets/sample_avatar.jpg"
    }
  ],
  "webhook_url": null,
  "webhook_response_code": null
}
```

This endpoint retrieves a specific request.

After creating an image request you will receive a uid. You can poll this endpoint to find out when the image has been rendered. When the status is `completed` the image url will be available in `image_url`.

### HTTP Request

`GET https://api.bannerbear.com/v2/images/<uid>`

### Parameters

Parameter | Description
--------- | -----------
uid | The uid of the request to retrieve


# Templates

## List Templates

> To list a project's templates:

```shell
curl "https://api.bannerbear.com/v2/templates"
  -H "Authorization: Bearer API_KEY"
```

> The above endpoint returns a JSON array like this:

```json
{
  "templates" : [
    {
      "created_at": "2020-03-17T01:58:07.358Z",
      "name": "Twitter Demo",
      "self": "https://api.bannerbear.com/v2/templates/04PK8K2bctXHjqB97O",
      "uid": "04PK8K2bctXHjqB97O",
      "preview_url": null,
      "width": 1000,
      "height": 1000,
      "available_modifications": [
        {
          "name": "avatar",
          "image_url": null
        },
        {
          "name": "name",
          "text": null
        },
        {
          "name": "handle",
          "text": null
        },
        {
          "name": "body",
          "text": null
        }
      ]
    }
  ]
}
```


This endpoint lists a project's templates. 

This endpoint may be useful for applications with many templates that need to show users a selection, before using the `Images` endpoint to generate an image based on a chosen template.

### HTTP Request

`GET https://api.bannerbear.com/v2/templates`

### Template Preview

Each time you edit a template in Bannerbear, a thumbnail is saved and made available in the `preview_url` attribute of the Template object.

## Get a Specific Template

> To get a template:

```shell
curl "https://api.bannerbear.com/v2/templates/04PK8K2bctXHjqB97O"
  -H "Authorization: Bearer API_KEY"
```

> The above endpoint returns JSON like this:

```json
{
  "created_at": "2020-03-17T01:58:07.358Z",
  "name": "Twitter Demo",
  "self": "https://api.bannerbear.com/v2/templates/04PK8K2bctXHjqB97O",
  "uid": "04PK8K2bctXHjqB97O",
  "preview_url": null,
  "width": 1000,
  "height": 1000,
  "available_modifications": [
    {
      "name": "avatar",
      "image_url": null
    },
    {
      "name": "name",
      "text": null
    },
    {
      "name": "handle",
      "text": null
    },
    {
      "name": "body",
      "text": null
    }
  ]
}
```

### HTTP Request

`GET https://api.bannerbear.com/v2/templates/<uid>`

### Parameters

Parameter | Description
--------- | -----------
uid | The uid of the template to retrieve

