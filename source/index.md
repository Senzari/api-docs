---
title: MusicGraph API Reference

toc_footers:
 - <center><a href='https://developer.musicgraph.com/#plans'>Sign Up for a Developer Key</a></center>
---

# Introduction

Welcome to the MusicGraph API documentation!

You can use the MusicGraph API to search through more than one billion music-related connections. Data is stored in a graph database, which you can query using familiar REST HTTP calls. This document specifies the set of HTTP methods exposed by the MusicGraph API.

All URLs pertaining to the MusicGraph API share a common base:

**`http://api.v2.musicgraph.com`**

## Authentication

All requests made to the MusicGraph API must be signed with an API key. The API key used in this document is for demonstration purposes only. It should not be used for any other application as it is rate limited and restricted by IP. You can [sign up for a free account here](https://developer.musicgraph.com/#plans "Sign Up for a Developer Key").

### Required Parameter

Name | Description
-----|------------
**api_key** | Your API key

## Sending Requests

> *Find the track id for Jimi Hendrix's "Voodoo Child"...*

```shell
$ curl "http://api.v2.musicgraph.com/api/v2/track/search?&artist_name=jimi+hendrix&name=voodoo+childlimit=1&fields=id&api_key=c26e63de67a52b71cfcb1b2fb63a14f2"
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
      "id":"9f2c42a8-bc4a-9e77-98fc-d3917d69d544"
    }
  ]
}
```

The API is implemented directly over HTTP. In general, HTTP GET methods are used for read-only operations while HTTP POST are reserved for requests that potentially modify state on the server.

Most queries support cursor-based pagination. The `offset` parameter offsets the start of each page by the number specified. The `limit` parameter limits the number of objects to be returned. The maximum allowable count is 100.

In the example to the right we are limiting the JSON response to include only the id field for the artist object. By default, all fields are returned when you make an API request. You can select what fields you want returned with the `fields` parameter. Try sending the example request without the `fields` paramter, see what happens!

### Optional Parameters

Name | Description
-----|------------
**fields** | Limit the set of returned properties; comma-separated
**limit** | Limit the number of results; max is 100, default is 15
**offset** | Paginate the results set

## Error Handling

> *The requested object could not be found...*

```shell
$ curl "http://api.v2.musicgraph.com/api/v2/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/tracks?api_key=c26e63de67a52b71cfcb1b2fb63a14f2"
```

```http
HTTP/1.1 404 Not Found
```

```json
{
  "status":{
    "api":"v2",
    "code":7,
    "message":"Invalid ID"
  }
}
```

> *Access denied because no API key was included in the request...*

```shell
$ curl "http://api.v2.musicgraph.com/api/v2/e36675b4-a6b5-11e0-b446-00251188dd67"
```

```http
HTTP/1.1 403 Forbidden
```

```json
{
  "status":{
    "api":"v2",
    "code":1,
    "message":"Missing or Invalid API Key"
  }
}
```

> *The request could not be understood by the server...*

```shell
$ curl "http://api.v2.musicgraph.com/api/v2/artist/search?q=madonna&api_key=c26e63de67a52b71cfcb1b2fb63a14f2"
```

```http
HTTP/1.1 400 Bad Request
```

```json
{
  "status":{
    "api":"v2",
    "code":5,
    "message":"Invalid Search Operation"
  }
}
```

The response you receive will vary based on the resource you are retrieving, but it will generally take the following form:

    {
      "status":{
        "api":"v2",
        "code":0,
        "message":"Success"
      },
      "data":[...]
    }

### HTTP Response Codes

The MusicGraph API uses standard HTTP/1.1 response codes as described in [RFC 2616](http://tools.ietf.org/html/rfc2616 "The HTTP/1.1 Standard") to indicate the success or failure of an API request. Codes in the 2xx range indicate success, codes in the 4xx and 5xx ranges indicate an error; for example, an invalid id, missing api key, or type exception.

Code | Message
-----|--------
`200` | Success
`400` | Bad Request - the request is not valid
`403` | Forbidden - you are not authorized to access that resource
`404` | Not Found - The requested resource could not be found
`429` | Too Many Requests - You have exceeded the rate limit associated with your API key
`500` | Internal Server Error - Please [email us](mailto:) to report a service outage

### Response Status Codes

The API uses an internal code to signal the status of an API request. Internal status codes are packaged in the JSON data of an API response. A status code of 0 indicates success. Any number besides zero indicates an error of some kind. Negative numbers are reserved for egregious errors.

Code | Message
-----|--------
`-1` | Internal Server Error
`0` | Success
`1` | Missing/ Invalid API Key
`2` | This API key is not allowed to call this method
`3` | Rate Limit Exceeded
`4` | Not Supported
`5` | Invalid Search Operation
`6` | Invalid Connection Name
`7` | Invalid MusicGraph ID
`8` | Invalid Type

## Versioning

The current version of the MusicGraph API (v2) was released on 2014-03-26. We release new versions of the API whenever backwards-incompatible changes are made.

## Terms And Conditions

We need to protect users, content providers, our software and service while at the same time enabling you to create applications. We therefore require you to comply with the following terms.

1. Do not use the MusicGraph API or its feeds in connection with anything that promotes or takes part in any products, services, or materials that Senzari might consider malicious, hateful, or illegal.

2. You may not sell, lease, share, transfer, or sublicense access to the MusicGraph API or its feeds to any party other than the API key holder.

Please read our [Terms Of Service](https://developer.musicgraph.com/policies "Terms Of Service") before you start developing anything using the MusicGraph API.

# Graph Search API

The Graph Search API allows you to dramatically expand your music service’s search capabilities, allowing your users to perform more powerful and more precise queries.

With Graph Search, a user can easily traverse our ever-expanding graph ontology that includes hundreds of millions of entities (vertices) and connections (edges), as well as seven billion properties, making it one of the richest collection of music knowledge in the world.

It is analogous to Facebook's Graph Search, but instead of responding higher-level questions about a given user’s social graph, like in "Photos of and friends or coworkers and your current city", Graph Search allows for deep queries within the music realm, as in for example "Artists similar to Rihanna, that are female and that have more than 100k followers in Twitter."

Try Graph Search today and let your users discover artists, albums and songs in the most powerful way possible.

## Artists

> *Find artists from the 1960s that sound similar to Jimi Hendrix...*

```shell
$ curl "http://api.v2.musicgraph.com/api/v2/artist/search?&decade=1960s&similar_to=Jimi+Hendrix&limit=3&fields=id,name&api_key=c26e63de67a52b71cfcb1b2fb63a14f2"
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
$ curl "http://api.v2.musicgraph.com/api/v2/artist/search?similar_to=Madonna&gender=female&limit=3&fields=id,name&api_key=c26e63de67a52b71cfcb1b2fb63a14f2"
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

The base URL path for artist queries is:

**`http://api.v2.musicgraph.com/api/v2/artist/search`**

### Artist Endpoints

Resource URL | Description
-------------|------------
`/api/v2/[id]` | Return an artist's metadata
`/api/v2/[id]/albums` | Return an artist's albums
`/api/v2/[id]/tracks` | Return an artist's tracks
`/api/v2/artist/search` | Execute a search operations (described below)

### Artist Search Operations

Parameter | Description
----------|------------
**name** | Return artists who match the name
**similar_to** | Return artists that are similar to the given name
**produced_by** | Return artists who worked with a given producer
**influenced** | Return artists who influenced a given artist
**influenced_by** | Return artists who were influenced by a given artist

### Artist Search Filters

Parameter | Description
-----|------------
**decade** | Return artists who were active during a given decade; see [possible values](#decade-values)
**gender** | Return artists filtered by gender; possible values are "Male" and "Female"
**genre** | Return artists filtered by genre; see [possible values](#musical-genres)
**influenced** | Return artists who influenced a given artist
**influenced_by** | Return artists who were influenced by a given artist

### Example Artist Endpoints

Description | Link To Example
------------|-----
**Artist Metadata** | [/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67](http://api.v2.musicgraph.com/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67?api_key=c26e63de67a52b71cfcb1b2fb63a14f2)
**Artist Edges** | [/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67/edges](http://api.v2.musicgraph.com/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67/edges?api_key=c26e63de67a52b71cfcb1b2fb63a14f2)
**Artist Albums** | [/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67/albums](http://api.v2.musicgraph.com/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67/albums?api_key=c26e63de67a52b71cfcb1b2fb63a14f2)
**Artist Tracks** | [/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67/tracks](http://api.v2.musicgraph.com/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67/tracks?api_key=c26e63de67a52b71cfcb1b2fb63a14f2)
**Artist Similars** | [/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67/similar](http://api.v2.musicgraph.com/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67/similar?api_key=c26e63de67a52b71cfcb1b2fb63a14f2)
**Artist URLs** | [/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67/social-urls](http://api.v2.musicgraph.com/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67/social-urls?api_key=c26e63de67a52b71cfcb1b2fb63a14f2)
**Limit Fields** | [/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67?fields=id,name](http://api.v2.musicgraph.com/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67?fields=id,name&api_key=c26e63de67a52b71cfcb1b2fb63a14f2)
**Artist Search: Name** | [/api/v2/artist/search?name=madonna](http://api.v2.musicgraph.com/api/v2/artist/search?name=madonna&api_key=c26e63de67a52b71cfcb1b2fb63a14f2)
**Artist Search: Similar To** | [/api/v2/artist/search?similar_to=madonna](http://api.v2.musicgraph.com/api/v2/artist/search?similar_to=madonna&api_key=c26e63de67a52b71cfcb1b2fb63a14f2)
**Artist Search: Produced By** | [/api/v2/artist/search?produced_by=madonna](http://api.v2.musicgraph.com/api/v2/artist/search?produced_by=madonna&api_key=c26e63de67a52b71cfcb1b2fb63a14f2)
**Artist Search: Influenced** | [/api/v2/artist/search?influenced=madonna](http://api.v2.musicgraph.com/api/v2/artist/search?influenced=madonna&api_key=c26e63de67a52b71cfcb1b2fb63a14f2)
**Artist Search: Influenced By** | [/api/v2/artist/search?influenced_by=madonna](http://api.v2.musicgraph.com/api/v2/artist/search?influenced_by=madonna&api_key=c26e63de67a52b71cfcb1b2fb63a14f2)
**Artist Filter: Decade** | [/api/v2/artist/search?similar_to=madonna&decade=1990s](http://api.v2.musicgraph.com/api/v2/artist/search?similar_to=madonna&decade=1990s&api_key=c26e63de67a52b71cfcb1b2fb63a14f2)
**Artist Filter: Genre** | [/api/v2/artist/search?similar_to=madonna&genre=Rap](http://api.v2.musicgraph.com/api/v2/artist/search?similar_to=madonna&genre=Rap&api_key=c26e63de67a52b71cfcb1b2fb63a14f2)
**Artist Filter: Gender** | [/api/v2/artist/search?similar_to=madonna&gender=male](http://api.v2.musicgraph.com/api/v2/artist/search?similar_to=madonna&gender=male&api_key=c26e63de67a52b71cfcb1b2fb63a14f2)
**Nested Search: Artist Albums** | [/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67?fields=id,name,albums.limit(100).fields(id)](http://api.v2.musicgraph.com/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67?fields=id,name,albums.limit(100).fields(id)&api_key=c26e63de67a52b71cfcb1b2fb63a14f2)
**Nested Search: Artist Tracks** | [/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67?fields=id,name,albums.limit(100).fields(id),tracks.limit(100).fields(title)](http://api.v2.musicgraph.com/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67?fields=id,name,albums.limit(100).fields(id),tracks.limit(100).fields(title)&api_key=c26e63de67a52b71cfcb1b2fb63a14f2)

## Albums

> *Find tracks featured on Michael Jackson's "Thriller" album...*

```shell
$ curl "http://api.v2.musicgraph.com/api/v2/18b44cbd-fa95-e03a-27c6-1fdc02395e63/tracks?fields=id,title&api_key=c26e63de67a52b71cfcb1b2fb63a14f2"
```

```http
HTTP/1.1 200 OK
```

```json
{
    "status": {
        "api": "v2",
        "code": 0,
        "message": "Success"
    },
    "total": 445,
    "offset": 1,
    "count": 15,
    "data": [
        {
            "id": "d9529a1d-9d9c-f676-00e6-4b37084705a3",
            "title": "Thriller"
        },
        {
            "id": "86fa1949-3e14-fa88-a8fa-a73208d27fb9",
            "title": "Human Nature"
        },
        {
            "id": "31b68615-e4a2-5c85-e19f-733150669101",
            "title": "Wanna Be Startin' Somethin'"
        },
        {
            "id": "7ab02613-038b-d5a9-ec40-0971568d182f",
            "title": "Lady in My Life"
        },
        {
            "id": "cd1f6dfc-eec5-baec-910e-f24a3c3d974e",
            "title": "Vincent Price Excerpt"
        },
        {
            "id": "ffe72498-96af-6806-0c5f-971fa6722cb8",
            "title": "P.Y.T. (Pretty Young Thing)"
        },
        {
            "id": "2e7bda58-3a9e-fc02-2b03-df1a9606945b",
            "title": "Lady in My Life"
        },
        {
            "id": "2721e4d7-0828-cb78-524c-4961207b36b4",
            "title": "Billie Jean"
        },
        {
            "id": "8dcf6ab6-29c3-5768-8a4f-1406aaf38a68",
            "title": "Beat It 2008"
        },
        {
            "id": "59d3efea-ca5a-bb33-12f0-37f8edb6e906",
            "title": "The Lady in My Life"
        },
        {
            "id": "2b4ff8c8-d4fc-dd22-846e-37846ab70c2c",
            "title": "Baby Be Mine"
        },
        {
            "id": "205d6f97-43b5-9464-e1ef-c08cac326bb6",
            "title": "Beat It"
        },
        {
            "id": "de324972-dfe3-577b-ef15-eea28219d6a8",
            "title": "For All Time"
        },
        {
            "id": "f491d58a-0872-a9d9-2a3d-0306191d829a",
            "title": "P.Y.T. (Pretty Young Thing)"
        }
    ]
}
```

The base URL path for album queries is:

**`http://api.v2.musicgraph.com/api/v2/album`**

### Album Endpoints

Resource URL | Description
-------------|------------
`/api/v2/[id]` | Return metadata for an album by id
`/api/v2/[id]/tracks` | Return the tracks that appear on an album
`/api/v2/album/search` | Execute a search operation (described below)

### Album Search Operations

Parameter | Description
----------|------------
**name** | Find albums by name
**artist_name** | Find albums by an artist
**produced_by** | Find albums that were produced by a producer
**similar_to** | Find albums that are similar to another album

### Album Search Filters

Parameter | Description
----------|------------
**country** | Filter album search results by country
**decade** | Filter album search results by release date; see [possible values](#decade-values)
**gender** | Filter album search results by artist gender; possible values are "Male" and "Female"
**genre** | Filter album search results by genre; see [possible values](#musical-genres)
**top_rated** | Return only top-rated albums; possible values are **true** and **false**

### Example Album Endpoints

Description | Endpoint
------------|---------
**album-meta** | `/api/v2/album/f0764c86-be44-bc6c-34a4-c899a1272f02?api_key=c26e63de67a52b71cfcb1b2fb63a14f2`
**album-tracks** | `/api/v2/album/f0764c86-be44-bc6c-34a4-c899a1272f02/tracks?api_key=c26e63de67a52b71cfcb1b2fb63a14f2`
**album-by-title** | `/api/v2/album/search?api_key=c26e63de67a52b71cfcb1b2fb63a14f2&name=Houses+Of+The+Holy`
**album-by-artist** | `/api/v2/album/search?api_key=c26e63de67a52b71cfcb1b2fb63a14f2&artist_name=John+Lennon`
**album-similar-to** | `/api/v2/album/search?api_key=c26e63de67a52b71cfcb1b2fb63a14f2&produced_by=Rick+Rubin`
**album-similar-to** | `/api/v2/album/search?api_key=c26e63de67a52b71cfcb1b2fb63a14f2&similar_to=Thriller`
**album-availops-similar-to** | `/api/v2/album/availableoperations?api_key=c26e63de67a52b71cfcb1b2fb63a14f2&similar_to=Thriller`
**album-similar-country** | `/api/v2/album/search?api_key=c26e63de67a52b71cfcb1b2fb63a14f2&similar_to=21&country=UK`
**album-similar-decade** | `/api/v2/album/search?api_key=c26e63de67a52b71cfcb1b2fb63a14f2&similar_to=21&decade=1990s`
**album-similar-genre** | `/api/v2/album/search?api_key=c26e63de67a52b71cfcb1b2fb63a14f2&similar_to=21&genre=Rap`
**album-similar-top-rated** | `/api/v2/album/search?api_key=c26e63de67a52b71cfcb1b2fb63a14f2&similar_to=Thriller&top_rated=true`
**album-availops-similar-top-rated** | `/api/v2/album/availableoperations?api_key=c26e63de67a52b71cfcb1b2fb63a14f2&similar_to=Thriller&top_rated=true`

## Tracks

> *Find tracks by Eminem that feature Rihanna as a guest vocalist...*

```shell
$ curl "http://api.v2.musicgraph.com/api/v2/track/search?artist_name=eminem&featuring_artist=rihanna&limit=1&fields=id,title,artist_name,album_title&api_key=c26e63de67a52b71cfcb1b2fb63a14f2"
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
      "id":"f4fe3ba0-c599-157f-3cc1-b45c038620eb",
      "title":"Love the Way You Lie",
      "artist_name":"Eminem",
      "album_title":"Recovery"
    }
  ]
}
```

> *Find tracks written by Eminem about his daughter, Hailie...*

```shell
$ curl "http://api.v2.musicgraph.com/api/v2/track/search?artist_name=eminem&lyrics_phrase=hailie&limit=5&fields=title&api_key=c26e63de67a52b71cfcb1b2fb63a14f2"
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
      "title":"Superman"
    },
    {
      "title":"You're Never Over"
    },
    {
      "title":"Hailie's Song"
    },
    {
      "title":"Mockingbird"
    },
    {
      "title":"Cleanin' Out My Closet"
    }
  ]
}
```

The base URL path for track queries is:

**`http://api.v2.musicgraph.com/api/v2/track`**

### Track Endpoints

Resource URL | Description
-------------|------------
`/api/v2/track/[id]` | Return a track's metadata
`/api/v2/track/search` | Execute a search operation (described below)

### Track Search Operations

Parameter | Description
----------|------------
**name** | Find tracks by name
**artist_name** | Find tracks by an artist
**composed_by** | Find tracks that were composed by an artist
**featuring_artist** | Find tracks that feature an artist

### Track Search Filters

Parameter | Description
----------|------------
**decade** | Filter track search results by release date; see [possible values](#decade-values)
**genre** | Filter track search results by genre; see [possible values](#musical-genres)
**lyrics_keywords** | Filter track search results by lyric keywords
**lyrics_lang** | Filter track search results by language; see [possible values](#language-codes)
**lyrics_phrase** | Filter track search results that contain an exact match

### Example Endpoints

Description | Endpoint
------------|---------
**track-meta** | `/api/v2/track/f05e067b-a6c0-11e0-b446-00251188dd67?api_key=c26e63de67a52b71cfcb1b2fb63a14f2`
**track-by-title** | `/api/v2/track/search?api_key=c26e63de67a52b71cfcb1b2fb63a14f2&name=Royals`
**track-artist_name** | `/api/v2/track/search?api_key=c26e63de67a52b71cfcb1b2fb63a14f2&artist_name=Pink`
**track-composed_by** | `/api/v2/track/search?api_key=c26e63de67a52b71cfcb1b2fb63a14f2&composed_by=Sting`
**track-featuring_artist** | `/api/v2/track/search?api_key=c26e63de67a52b71cfcb1b2fb63a14f2&featuring_artist=Rihanna`
**track-availops-featuring_artist** | `/api/v2/track/**availableoperations?api_key=c26e63de67a52b71cfcb1b2fb63a14f2&featuring_artist=Rihanna`
**track-artist-decade** | `/api/v2/track/search?api_key=c26e63de67a52b71cfcb1b2fb63a14f2&artist_name=Lorde&decade=2010s`
**track-inspired-genre** | `/api/v2/track/search?api_key=c26e63de67a52b71cfcb1b2fb63a14f2&artist_name=Shakira&genre=New Age`

# Playlist API

> *Search for a playlist with Pearl Jam as the seed artist...*

```shell
$ curl "http://api.v2.musicgraph.com/api/v2/playlist?artist_id=e2ffceb5-a6b5-11e0-b446-00251188dd67&fields=id,title,artist_name&limit=3&api_key=c8e73991124fd6b5e8a62c4f7160496d"
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
      "id":"f1deb0dc-7ffd-8d9f-e98b-76180743da8f",
      "title":"Elderly Woman Behind The Counter In A Small Town",
      "artist_name":"Pearl Jam"
    },
    {
      "id":"8138636f-02a6-9a61-1b22-46f80b6039d6",
      "title":"Lithium",
      "artist_name":"Nirvana"
    },
    {
      "id":"3314061f-a6c0-11e0-b446-00251188dd67",
      "title":"Jeremy",
      "artist_name":"Pearl Jam"
    }
  ]
}
```

The Playlist API allows you to create powerful DMCA-compliant music recommendations based on hundreds of thousands of potential "seed artists."

One importantly differentiator in the way we generate our recommendations is that we use very powerful "graph algorithms" to select the best songs in a real-time for each user, which can take into account a large number of variables, such as musical similarities, personal taste, social relationships, market metrics, user context, etc.

These "graph recommendation" approach not only results in much more personalized selections, but also in a more transparent experience for the end-user, as each recommendation comes with “evidence” that clearly explains why each song was selected. We can achieve this by "walking the graph" and taking note of each step taken in the decision process. What is even cooler is that depending on the user feedback, we can modify the "walk", so that for example we can skip the "social nodes" in the event that the user hates his or her friends' taste in music.

The base URL path for playlist queries is:

**`http://api.v2.musicgraph.com/api/v2/playlist`**

### Playlist Endpoint

Resource URL | Description
-------------|------------
`/api/v2/playlist` | Recommend a personalized playlist

### Playlist Parameters

Parameter | Description
----------|------------
**artist_id** | Generate a playlist with a seed artist

### Example Endpoints

Description | Endpoint
------------|---------
**playlist-artist-id** | `/api/v2/playlist?api_key=c8e73991124fd6b5e8a62c4f7160496d&artist_id=ee2564c7-a6b5-11e0-b446-00251188dd67`

# Music Data API

The Music Data API provides detailed data about:

- **Artists' social media trends** - Followers, song plays and video views across leading sources such as Twitter, Facebook, LastFM and Vevo.
- **Lyrical features** - for licensing reasons, rather than returning the full lyrics in a song, we extract and return the top 15 most "significant" words per song, hopefully giving you a general idea of what the song is about. A language code is also included in the results.

For more details please [check out our plans](https://developer.musicgraph.com/#plans) and get your free API key today.

## Artist Metrics Data

> *Search for metrics using Pearl Jam as the artist...*

```shell
$ curl "http://api.v2.musicgraph.com/api/v2/e2ffceb5-a6b5-11e0-b446-00251188dd67/metrics?&api_key=fcffa99e795c2f3996c843fd8069ee36"
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
  "total":1,
  "count":1,
  "data":[
    {
      "id":"e2ffceb5-a6b5-11e0-b446-00251188dd67",
      "twitter":{
        "followers":{
          "data":{
            "time":1395361433197,
            "value":2409521
          }
        },
        "following":{
          "data":{
            "time":1395361433197,
            "value":427
          }
        },
        "tweets":{
          "data":{
            "time":1395361433197,
            "value":2360
          }
        },
        "url":"https://twitter.com/pearljam"
      },
      "lastfm":{
        "listeners":{
          "data":{
            "time":1394556777947,
            "value":2400627
          }
        },
        "playcount":{
          "data":{
            "time":1394556777947,
            "value":117438534
          }
        },
        "url":"http://www.last.fm/music/Pearl+Jam"
      },
      "name":"Pearl Jam",
      "facebook":{
        "likes":{
          "data":{
            "time":1395361446281,
            "value":9526470
          }
        },
        "url":"https://www.facebook.com/pearljam",
        "people_talking_about":{
          "data":{
            "time":1395361446281,
            "value":192778
          }
        }
      },
      "vevo":{
        "viewsLastDay":{
          "data":{
            "time":1395361446360,
            "value":35765
          }
        },
        "viewsLastMonth":{
          "data":{
            "time":1395361446360,
            "value":1901633
          }
        },
        "viewsLastWeek":{
          "data":{
            "time":1395361446360,
            "value":421046
          }
        },
        "viewsTotal":{
          "data":{
            "time":1395361446360,
            "value":99008399
          }
        },
        "url":"na"
      }
    }
  ],
  "offset":1
}
```

> *If you want to only view a specific data sources data, then pass the source name in the URL as shown below...*

```shell
$ curl "http://api.v2.musicgraph.com/api/v2/e2ffceb5-a6b5-11e0-b446-00251188dd67/metrics/twitter?api_key=fcffa99e795c2f3996c843fd8069ee36"
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
  "total":1,
  "count":1,
  "data":[
    {
      "id":"e2ffceb5-a6b5-11e0-b446-00251188dd67",
      "twitter":{
        "followers":{
          "data":{
            "time":1395361433197,
            "value":2409521
          }
        },
        "following":{
          "data":{
            "time":1395361433197,
            "value":427
          }
        },
        "tweets":{
          "data":{
            "time":1395361433197,
            "value":2360
          }
        },
        "url":"https://twitter.com/pearljam"
      },
      "name":"Pearl Jam"
    }
  ],
  "offset":1
}
```

The base URL path for artist metrics queries is:

**`http://api.v2.musicgraph.com/api/v2/artist/[id]/metrics`**

### Artist Metrics Endpoints

Resource URL | Description
-------------|------------
`/api/v2/artist/[id]/metrics` | Retrieve all available metrics data
`/api/v2/artist/[id]/metrics/facebook` | Retrieve Facebook metrics data
`/api/v2/artist/[id]/metrics/lastfm` | Retrieve Last FM metrics data
`/api/v2/artist/[id]/metrics/twitter` | Retrieve Twitter metrics data
`/api/v2/artist/[id]/metrics/vevo` | Retrieve Vevo metrics data

### Example Endpoints

Description | Endpoint
------------|---------
**artist-metrics** | `/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67/metrics?api_key=fcffa99e795c2f3996c843fd8069ee36`
**artist-metrics-facebook** | `/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67/metrics/facebook?api_key=fcffa99e795c2f3996c843fd8069ee36`
**artist-metrics-lastfm** | `/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67/metrics/lastfm?api_key=fcffa99e795c2f3996c843fd8069ee36`
**artist-metrics-twitter** | `/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67/metrics/twitter?api_key=fcffa99e795c2f3996c843fd8069ee36`
**artist-metrics-vevo** | `/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67/metrics/vevo?api_key=fcffa99e795c2f3996c843fd8069ee36`

## Track Lyrical Features

> *Retrieve lyrical features for Adele's "Right As Rain"...*

```shell
$ curl "http://api.v2.musicgraph.com/api/v2/eaaf2cb4-c9d3-4715-0663-861709eff48d/lyrical_features?api_key=fcffa99e795c2f3996c843fd8069ee36"
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
  "total":1,
  "count":1,
  "data":[
    {
      "id":"eaaf2cb4-c9d3-4715-0663-861709eff48d",
      "title":"Right as Rain",
      "album":"Live at the Royal Albert Hall",
      "language":"en",
      "bow":[
        "concerned",
        "as",
        "wipe",
        "excitement",
        "off",
        "cried",
        "dirty",
        "rain",
        "wants",
        "tired",
        "chose",
        "far",
        "pay",
        "when",
        "bed"
      ],
      "artist":"Adele"
    }
  ],
  "offset":1
}
```

The base URL path for track lyrical features is:

**`http://api.v2.musicgraph.com/api/v2/track/[id]/lyrical_features`**

### Track Lyrical Features Endpoints

Resource URL | Description
-------------|------------
`/api/v2/track/[id]/lyrical_features` | Retrieve lyrical features for a track

# Type Dictionary

## Country Codes

Code | Description
-----|------------
**AR** | Argentina
**AU** | Australia
**BR** | Brazil
**BS** | Bahamas
**CA** | Canada
**CH** | Switzerland
**CL** | Chile
**CN** | China
**CO** | Colombia
**DE** | Germany
**EG** | Egypt
**ES** | Spain
**FR** | France
**GB** | United Kingdom
**GR** | Greece
**IE** | Ireland
**IL** | Israel
**IR** | Iran
**IT** | Italy
**JP** | Japan
**MX** | Mexico
**NL** | Netherlands
**RO** | Romania
**RU** | Russia
**US** | United States

<aside class="notice">The API uses standard 2-letter [ISO 3166-1 country codes](http://en.wikipedia.org/wiki/ISO_3166-1_alpha-2).</aside>

## Decade Values

<table>
<tr><th>Value</th></tr>
<tr><td>1890s</td></tr>
<tr><td>1900s</td></tr>
<tr><td>1910s</td></tr>
<tr><td>1920s</td></tr>
<tr><td>1930s</td></tr>
<tr><td>1940s</td></tr>
<tr><td>1950s</td></tr>
<tr><td>1960s</td></tr>
<tr><td>1970s</td></tr>
<tr><td>1980s</td></tr>
<tr><td>1990s</td></tr>
<tr><td>2000s</td></tr>
<tr><td>2010s</td></tr>
</table>

## Language Codes

Code | Description
------|------------
**DE** | German
**EN** | English
**ES** | Spanish
**FR** | French
**IT** | Italian
**NL** | Dutch
**PT** | Portuguese
**RU** | Russian
**SV** | Swedish

## Musical Genres

<table>
<tr><th>Value</th></tr>
<tr><td>Avant-Garde</td></tr>
<tr><td>Blues</td></tr>
<tr><td>Children's</td></tr>
<tr><td>Classical</td></tr>
<tr><td>Comedy/Spoken</td></tr>
<tr><td>Country</td></tr>
<tr><td>Easy Listening</td></tr>
<tr><td>Electronic</td></tr>
<tr><td>Folk</td></tr>
<tr><td>Holiday</td></tr>
<tr><td>International</td></tr>
<tr><td>Jazz</td></tr>
<tr><td>Latin</td></tr>
<tr><td>New Age</td></tr>
<tr><td>Pop/Rock</td></tr>
<tr><td>R&B</td></tr>
<tr><td>Rap</td></tr>
<tr><td>Reggae</td></tr>
<tr><td>Religious</td></tr>
<tr><td>Stage & Screen</td></tr>
<tr><td>Vocal</td></tr>
</table>
