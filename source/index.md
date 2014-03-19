---
title: API Reference

toc_footers:
 - <a href='https://developer.musicgraph.com/#plans'>Sign Up for a Developer Key</a>
 - <a href='http://github.com/tripit/slate'>Documentation Powered by Slate</a>
---

# Getting Started

Welcome to the MusicGraph API documentation! You can use the MusicGraph API to search through more than one billion music-related connections. Data is stored in a graph structure that you can query using familiar REST HTTP calls. This document specifies the set of HTTP methods exposed by the MusicGraph API.

All requests made to the MusicGraph API must be authenticated with an API key. The API key used in this document is for demonstration purposes only. It should not be used for any other application as it is rate limited. You can [sign up for a free account here](https://developer.musicgraph.com/#plans "Sign Up for a Developer Key").

## Sending Requests

> *Find the track id for Jimi Hendrix's "Voodoo Child"...*

```shell
$ curl "http://api.musicgraph.com/api/v2/track/search?&artist_name=jimi+hendrix&name=voodoo+childlimit=1&fields=id&api_key=APIKEY"
```

```http
HTTP/1.1 200 OK
```

```json
{
  "status":{
    "api":"v1",
    "code":0,
    "message":"Success"
  },
  "data":[
    {
      "id":"9f2c42a8-bc4a-9e77-98fc-d3917d69d544"
    }
  ]
}
```

The MusicGraph API is strongly typed. In addition to having a type, objects stored in the graph also have a unique ID. Most objects support a metadata lookup. You can retrieve an object's metadata using its ID.

<aside class="notice">
In the example to the right we are limiting the JSON response to include only the id field for the artist object. Try sending the request without the `fields` paramter, see what happens!
</aside>

### Required Parmeter

Name | Description
-----|------------
**api_key** | Your API key

### Optional Parameters

Name | Description
-----|------------
**fields** | Limit the set of returned properties; comma-separated
**limit** | Limit the number of results (max = 100; default = 15)
**offset** | Paginate the results set

## Error Handling

> *The requested object could not be found...*

```shell
$ curl "http://api.musicgraph.com/api/v2/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/tracks?api_key=APIKEY"
```

```http
HTTP/1.1 404 Not Found
```

```json
{
  "status":{
    "api":"v1",
    "code":7,
    "message":"Invalid ID"
  }
}
```

> *Access denied because no API key was included in the request...*

```shell
$ curl "http://api.musicgraph.com/api/v2/e36675b4-a6b5-11e0-b446-00251188dd67"
```

```http
HTTP/1.1 403 Forbidden
```

```json
{
  "status":{
    "api":"v1",
    "code":1,
    "message":"Missing or Invalid API Key"
  }
}
```

> *The request could not be understood by the server...*

```shell
$ curl "http://api.musicgraph.com/api/v2/artist/search?q=madonna&api_key=APIKEY"
```

```http
HTTP/1.1 400 Bad Request
```

```json
{
  "status":{
    "api":"v1",
    "code":5,
    "message":"Invalid Search Operation"
  }
}
```

The MusicGraph API uses standard HTTP/1.1 response codes as described in [RFC 2616](http://tools.ietf.org/html/rfc2616 "The HTTP/1.1 Standard") to indicate the success or failure of an API request. Codes in the 2xx range indicate success, codes in the 4xx and 5xx ranges indicate an error; for example, an invalid id, missing api key, or type exception.

### HTTP Response Codes

Code | Message
-----|--------
`200`| Success
`400` | Bad Request - the request is not valid
`403` | Forbidden - you are not authorized to access that resource
`404` | Not Found - The requested resource could not be found
`429` | Too Many Requests - You have exceeded the rate limit associated with your API key
`500` | Internal Server Error - Please [email us](mailto:) to report a service outage

### Status Codes

Code | Message
-----|--------
**-1** | Internal Server Error
**0** | Success
**1** | Missing/ Invalid API Key
**2** | This API key is not allowed to call this method
**3** | Rate Limit Exceeded
**4** | Not Supported
**5** | Invalid Search Operation
**6** | Invalid Connection Name
**7** | Invalid MusicGraph ID
**8** | Invalid Type


# Graph Search

## Artist API

> *Find artists from the 1960s that sound similar to Jimi Hendrix...*

```shell
$ curl "http://api.musicgraph.com/api/v2/artist/search?&decade=1960s&similar_to=Jimi+Hendrix&limit=3&fields=id,name"
```

```http
HTTP/1.1 200 OK
```

```json
{
  "status":{
    "api":"v2",
    "code":0,
    "message":"Success"
  },
  "data":[
    {
      "id":"629fcb4d-ae9e-dfde-593c-f75d7659ee9d",
      "name":"Frank Zappa"
    },
    {
      "id":"e4b5e4d8-a6b5-11e0-b446-00251188dd67",
      "name":"Grateful Dead"
    },
    {
      "id":"ee29f4f9-a6b5-11e0-b446-00251188dd67",
      "name":"Leslie West"
    }
  ]
}
```

> *Search for female artists that are similar to Madonna...*

```shell
$ curl "http://api.musicgraph.com/api/v2/artist/search?similar_to=Madonna&gender=female&limit=3&fields=id,name"
```

```http
HTTP/1.1 200 OK
```

```json
{
  "status":{
    "api":"v2",
    "code":0,
    "message":"Success"
  },
  "data":[
    {
      "id":"7d8fc1bf-e7c5-4471-7b3a-bef5a9d25249",
      "name":"Vanity"
    },
    {
      "id":"d64bcb0d-7c57-3099-07b7-bd47692ff2ab",
      "name":"Cyndi Lauper"
    },
    {
      "id":"e36675b4-a6b5-11e0-b446-00251188dd67",
      "name":"Gwen Guthrie"
    }
  ]
}
```

`GET http://api.musicgraph.com/api/v2/artist/search`

### Search Operations

Name | Description
-----|------------
**name** | Return artists who match the name
**similar_to** | Return artists that are similar to the given name
**produced_by** | Return artists who worked with a given producer
**influenced** | Return artists who influenced a given artist
**influenced_by** | Return artists who were influenced by a given artist

### Search Filters

Name | Description
-----|------------
**decade** | Return artists who were active during a given decade; [possible values]()
**gender** | Return artists filtered by genre; [possible values]()
**genre** | Return artists filtered by gender; [possible values]()
**influenced** | Return artists who influenced a given artist
**influenced_by** | Return artists who were influenced by a given artist

### Artist Metadata

Endpoint | Description
---------|------------
`/api/v2/<artist_id>` | Return metadata for this artist
`/api/v2/<artist_id>/albums` | Return albums by this artist
`/api/v2/<artist_id>/tracks` | Return tracks by this artist

# Terms And Conditions

We need to protect users, content providers, our software and service while at the same time enabling you to create applications. We therefore require you to comply with the following terms.

1. Do not use the MusicGraph API or its feeds in connection with anything that promotes or takes part in any products, services, or materials that Senzari might consider malicious, hateful, or illegal.

2. You may not sell, lease, share, transfer, or sublicense access to the MusicGraph API or its feeds to any party other than the API key holder.

Please read our [Terms Of Service](https://developer.musicgraph.com/policies "Terms Of Service") before you start developing anything using the MusicGraph API.
