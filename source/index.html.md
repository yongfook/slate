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
3. You use this API to generate variations of the template in PNG format

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

The `Images` resource is the main resource in Bannerbear. Everything you can do with the Bannerbear API is related in some way to creating `Images`.

The `Image` object represents an image you have requested to generate. You generate images by sending a `POST` request to Bannerbear with a template uid and a list of template modifications you want to apply.

## Create an Image

> To create an image:

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
  "webhook_response_code": null,
  "transparent": false,
  "metadata": null
}
```

This endpoint creates a new `Image`.

### HTTP Request

`POST https://api.bannerbear.com/v2/images`

### Post Parameters

Send as a JSON object

Parameter | Type | Description
--------- | ------- | -----------
template | string | The template uid you want to use
modifications | array | A list of Modifications you want to make
webhook_url<br /><span class="tag">optional</span> | string | A url to POST the `Image` object to upon rendering completed
transparent<br /><span class="tag">optional</span> | boolean | Render a PNG with a transparent background. Defaults to `false` (white backround).
metadata<br /><span class="tag">optional</span> | string | Metadata that you need to store e.g. ID of a record in your DB

You can view a sample API payload for any template in the Bannerbear API Console.


## Modifications Array

> Example modifications:

```json
{
  "modifications": [
    {
      "name": "title",
      "text": "Lorem ipsum dolor sit amed"
    },
    {
      "name": "avatar",
      "image_url": "https://www.bannerbear.com/assets/sample_avatar.jpg"
    }
  ]
}
```

Your `Image` API payload should contain a parameter named `modifications` which is an array of modifications you would like to apply to a template.

All properties are optional, for example if you do not specify a color, the object's default color will be used. 

Property | Type | Description
--------- | ------- | -----------
name | string | The name of the item you want to change
text | string | Replacement text you want to use
color | string | Color hex of object e.g. "#FF0000"
background | string | Color hex of text background
image_url | string | Replacement image url you want to use (must be publicly viewable)
fit | boolean | When set to `true` will resize up text to fit the `max height` of the text box.<br /><span class="tag">experimental</span>

### Text Autoresizing

To use text autoresizing, set a `max height` for text boxes in the template editor. When this is set, text that overflows the `max height` will be *autoresized down*.

For scenarios where you want text to always fill a `max height` text box (scaling up if text is too small and scaling down if text is too large) pass `fit: true` when making the API request.

## Image Response

All images are created with the status `pending`.

Images are usually rendered within a few seconds. When completed, the status changes to `completed`.

You can poll the GET endpoint for status updates. The `self` property of the response provides this endpoint.

### Image_url

When `completed` the image url is found in the `image_url` property of the `Image` object. It will be in PNG format.

*A legacy property named image_url_png also exists but you may ignore this as it is the same url as above.*

### Webhooks

Instead of polling, we recommended you define a webhook in `webhook_url` which Bannerbear will POST the `Image` object to after image rendering is complete.

To secure your webhook endpoint, Bannerbear will POST with the following header:

`Authorization: Bearer WEBHOOK_KEY`

<aside class="success">
Bannerbear will replace <code>WEBHOOK_KEY</code> with your project's webhook key
</aside>

You can find your project's webhook key in its settings page.

###Hotlinking

You may hotlink to `image_url` in your application.

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
  "webhook_response_code": null,
  "transparent": false,
  "metadata": null
}
```

This endpoint retrieves a specific `Image`.

After creating an image request you will receive a uid. You can poll this endpoint to find out when the image has been rendered. When the status is `completed` the image url will be available in `image_url`.

### HTTP Request

`GET https://api.bannerbear.com/v2/images/<uid>`

### Parameters

Parameter | Description
--------- | -----------
uid | The uid of the image to retrieve

# Templates

`Templates` are the reusable designs that you create in the Bannerbear editor. You can list a project's templates via the API.

## List Templates

> To list a project's templates:

```shell
curl "https://api.bannerbear.com/v2/templates"
  -H "Authorization: Bearer API_KEY"
```

> The above endpoint returns a JSON array like this:

```json
[
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
```


This endpoint lists a project's `Templates`. 

This endpoint may be useful for applications with many templates that need to show users a selection, before using the `Images` endpoint to generate an image based on a chosen template.

### HTTP Request

`GET https://api.bannerbear.com/v2/templates`

### Template Preview

Each time you edit a template in Bannerbear, a thumbnail is saved and made available in the `preview_url` property of the Template object.

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


# Collections

Sometimes you want to use the same data payload but push to multiple templates at once. In Bannerbear, you can do this by grouping templates together in a Template Set. Pushing data to a Template Set results in a response that includes multiple images (depending on how many templates were in your set). 

This multi-image object is known as a `Collection`.

## Create a Collection

> To create a collection:

```shell
curl "https://api.bannerbear.com/v2/collections"
  -H "Authorization: Bearer API_KEY"
  -H "Content-Type: application/json" 
  -d json
```

> The above endpoint returns JSON like this:

```json
{
  "created_at": "2020-07-31 04:28:32 UTC",
  "uid": "rZdpMYmAnDB1zb3kXL",
  "self": "https://api.bannerbear.com/v2/collections/rZdpMYmAnDB1zb3kXL",
  "template_set": "Dbl5xYVgKRzLwaNdqo",
  "status": "completed",
  "modifications": [
    {
      "name": "text_container",
      "text": "Hello World"
    }
  ],
  "webhook_url": null,
  "webhook_response_code": null,
  "image_urls": {
    "template_EagXkA3DwM1ZW2VBYw_image_url": "https://cdn.bannerbear.com/...",
    "template_197xPQmDnLqZG3E82Y_image_url": "https://cdn.bannerbear.com/..."
  },
  "images": [
    //array of Image objects
  ]
}
```

This endpoint creates a new `Collection`.

### HTTP Request

`POST https://api.bannerbear.com/v2/collections`

### Post Parameters

Send as a JSON object

Parameter | Type | Description
--------- | ------- | -----------
template_set | string | The template set uid you want to use
modifications | array | A list of Modifications you want to make
webhook_url<br /><span class="tag">optional</span> | string | A url to POST the `Collection` object to upon rendering completed

For more information on the modifications parameter, see the `Images` endpoint.

### Status

All collections are created with the status `pending`.

Collections are usually rendered within a few seconds. When completed, the status changes to `completed`.

You can poll the GET endpoint for status updates. The `self` property of the response provides this endpoint. Alternatively you can provide a webhook to get notified when generation is completed.



## Get a Specific Collection

> To get a collection:

```shell
curl "https://api.bannerbear.com/v2/collections/rZdpMYmAnDB1zb3kXL"
  -H "Authorization: Bearer API_KEY"
```

> The above endpoint returns JSON like this:

```json
{
  "created_at": "2020-07-31 04:28:32 UTC",
  "uid": "rZdpMYmAnDB1zb3kXL",
  "self": "https://api.bannerbear.com/v2/collections/rZdpMYmAnDB1zb3kXL",
  "template_set": "Dbl5xYVgKRzLwaNdqo",
  "status": "completed",
  "modifications": [
    {
      "name": "text_container",
      "text": "Hello World"
    }
  ],
  "webhook_url": null,
  "webhook_response_code": null,
  "image_urls": {
    "template_EagXkA3DwM1ZW2VBYw_image_url": "https://cdn.bannerbear.com/...",
    "template_197xPQmDnLqZG3E82Y_image_url": "https://cdn.bannerbear.com/..."
  },
  "images": [
    //array of Image objects
  ]
}
```

### HTTP Request

`GET https://api.bannerbear.com/v2/collections/<uid>`

### Parameters

Parameter | Description
--------- | -----------
uid | The uid of the collection to retrieve


# Crawls

The `Crawl` object represents a crawl you have requested to start. A `Crawl` extracts data from a webpage in order to generate an `Image`. `Crawls` take a url and crawler uid as input. The rules for data extraction are held in the `Crawler` object.

## Start a Crawl

> To create a request:

```shell
curl "https://api.bannerbear.com/v2/crawls"
  -H "Authorization: Bearer API_KEY"
  -H "Content-Type: application/json" 
  -d json
```

> The above endpoint returns JSON like this:

```json
{
  "url": "https://www.mywebsite.com/my-page.html",
  "created_at": "2020-06-08T07:32:23.902Z",
  "self": "https://api.bannerbear.com/v2/crawls/W7ErAeVjZAb6G1MpNQ",
  "uid": "W7ErAeVjZAb6G1MpNQ",
  "status": "pending",
  "image": null,
  "crawler": "pw2D9Ab16lEl0ayGRN"
}
```

This endpoint creates a new `Crawl`.

### HTTP Request

`POST https://api.bannerbear.com/v2/crawls`

### Post Parameters

Send as a JSON object

Parameter | Type | Description
--------- | ------- | -----------
crawler | string | The crawler uid you want to use
url | string | A publicly-viewable url you want to crawl
webhook_url<br /><span class="tag">optional</span> | string | A url to POST the `Image` object to upon rendering completed

### Status

All crawls are created with the status `pending`.

End to end, a crawl usually takes a few seconds. 

The url is crawled, after which an `Image` generation operation starts. When finished, the status changes to `completed` and the full `Image` object is available in the image property of the `Crawl` object.

You can poll the GET endpoint for status updates. The `self` property of the response provides this endpoint.

### Webhooks

Instead of polling, we recommended you define a webhook in `webhook_url` which Bannerbear will POST the `Image` object to after image rendering is complete. The rendered image url is found in the `image_url` property of the `Image` object.

## Get a Specific Crawl

> To get a crawl:

```shell
curl "https://api.bannerbear.com/v2/crawls/W7ErAeVjZAb6G1MpNQ"
  -H "Authorization: Bearer API_KEY"
```

> The above endpoint returns JSON like this:

```json
{
  "url": "https://www.mywebsite.com/my-page.html",
  "created_at": "2020-06-08T07:32:23.902Z",
  "self": "https://api.bannerbear.com/v2/crawls/W7ErAeVjZAb6G1MpNQ",
  "uid": "W7ErAeVjZAb6G1MpNQ",
  "status": "completed",
  "image": {
    "created_at": "2020-06-09T03:09:41.306Z",
    "status": "completed",
    "self": "https://api.bannerbear.com/v2/images/yMRj52Zwoa6xZ5YxWkdO3eE9P",
    "uid": "yMRj52Zwoa6xZ5YxWkdO3eE9P",
    "image_url": "https://cdn.bannerbear.com/...",
    "template": "p6jq8BX57gY5nlwWaK",
    "modifications": [
      {
        "name": "title",
        "text": "This is a title"
      }
    ],
    "webhook_url": null,
    "webhook_response_code": null,
    "metadata": null
  },
  "crawler": "pw2D9Ab16lEl0ayGRN"
}
```

This endpoint retrieves a specific `Crawl`.

After starting a crawl you will receive a uid. You can poll this endpoint to find out when the image has been rendered from the crawl. When the status is `completed` the full `Image` object will be available in the image key of the Crawl object.

### HTTP Request

`GET https://api.bannerbear.com/v2/crawls/<uid>`

### Parameters

Parameter | Description
--------- | -----------
uid | The uid of the crawl to retrieve

# Crawlers

The `Crawler` object represents a set of CSS selectors to use for data extraction from a website. `Crawler` configuration can be edited via the Bannerbear control panel.

`Crawlers` belong to a `Template` which is included in the response object.

## List Crawlers

> To list a project's crawlers:

```shell
curl "https://api.bannerbear.com/v2/crawlers"
  -H "Authorization: Bearer API_KEY"
```

> The above endpoint returns a JSON array like this:

```json
[
  {
    "domain": "mywebsite.com",
    "created_at": "2020-06-08T01:39:31.101Z",
    "self": "https://api.bannerbear.com/v2/crawlers/pw2D9Ab16lEl0ayGRN",
    "uid": "pw2D9Ab16lEl0ayGRN",
    "config": {
      "poster": {
        "selector": "meta[name=\"twitter:image:src\"]",
        "attribute": "content"
      },
      "title": {
        "selector": "h1"
      }
    },
    "template": {
      "created_at": "2020-06-05T21:59:34.361Z",
      "name": "Template 25",
      "self": "https://api.bannerbear.com/v2/templates/p6jq8BX57gY5nlwWaK",
      "uid": "p6jq8BX57gY5nlwWaK",
      "preview_url": "https://cdn.bannerbear.com/...",
      "width": 1200,
      "height": 630,
      "available_modifications": [
        {
          "name": "poster",
          "image_url": null
        },
        {
          "name": "title",
          "text": null,
          "color": null,
          "background": null
        }
      ]
    }
  }
]
```
This endpoint lists a project's available `Crawlers`.

### HTTP Request

`GET https://api.bannerbear.com/v2/crawlers`

## Get a Specific Crawler

> To get a crawler:

```shell
curl "https://api.bannerbear.com/v2/crawlers/pw2D9Ab16lEl0ayGRN"
  -H "Authorization: Bearer API_KEY"
```

> The above endpoint returns JSON like this:

```json
{
  "domain": "mywebsite.com",
  "created_at": "2020-06-08T01:39:31.101Z",
  "self": "https://api.bannerbear.com/v2/crawlers/pw2D9Ab16lEl0ayGRN",
  "uid": "pw2D9Ab16lEl0ayGRN",
  "config": {
    "poster": {
      "selector": "meta[name=\"twitter:image:src\"]",
      "attribute": "content"
    },
    "title": {
      "selector": "h1"
    }
  },
  "template": {
    "created_at": "2020-06-05T21:59:34.361Z",
    "name": "Template 25",
    "self": "https://api.bannerbear.com/v2/templates/p6jq8BX57gY5nlwWaK",
    "uid": "p6jq8BX57gY5nlwWaK",
    "preview_url": "https://cdn.bannerbear.com/...",
    "width": 1200,
    "height": 630,
    "available_modifications": [
      {
        "name": "poster",
        "image_url": null
      },
      {
        "name": "title",
        "text": null,
        "color": null,
        "background": null
      }
    ]
  }
}
```

### HTTP Request

`GET https://api.bannerbear.com/v2/crawlers/<uid>`

### Parameters

Parameter | Description
--------- | -----------
uid | The uid of the crawler to retrieve