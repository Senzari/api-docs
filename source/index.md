---
title: MusicGraph API Reference

toc_footers:
 - <a href='https://developer.musicgraph.com/#plans'>Sign Up for a Developer Key</a>
---

# Overview

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
    "api":"v2",
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
    "api":"v2",
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
    "api":"v2",
    "code":5,
    "message":"Invalid Search Operation"
  }
}
```

The MusicGraph API uses standard HTTP/1.1 response codes as described in [RFC 2616](http://tools.ietf.org/html/rfc2616 "The HTTP/1.1 Standard") to indicate the success or failure of an API request. Codes in the 2xx range indicate success, codes in the 4xx and 5xx ranges indicate an error; for example, an invalid id, missing api key, or type exception.

### HTTP Response Codes

Code | Message
-----|--------
`200` | Success
`400` | Bad Request - the request is not valid
`403` | Forbidden - you are not authorized to access that resource
`404` | Not Found - The requested resource could not be found
`429` | Too Many Requests - You have exceeded the rate limit associated with your API key
`500` | Internal Server Error - Please [email us](mailto:) to report a service outage

### JSON Status Codes

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

# Search API

## Artists

> *Find artists from the 1960s that sound similar to Jimi Hendrix...*

```shell
$ curl "http://api.musicgraph.com/api/v2/artist/search?&decade=1960s&similar_to=Jimi+Hendrix&limit=3&fields=id,name&api_key=APIKEY"
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
$ curl "http://api.musicgraph.com/api/v2/artist/search?similar_to=Madonna&gender=female&limit=3&fields=id,name&api_key=APIKEY"
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

### Artist Metadata Endpoints

Method | Endpoint | Description
-------|----------|------------
`GET` | `/<id>` | Retrieve metadata for an artist with an artist id
`GET` | `/<id>/albums` | List albums by this with the `/albums` connection
`GET` | `/<id>/tracks` | List tracks by this artist with the `/tracks` connection

### Artist Search Endpoint

Method | Endpoint | Description
-------|----------|------------
`GET` | `/artist/search` | Execute any number of search operations (described below)

### Artist Search Operations

Parameter | Description
-----|------------
**name** | Return artists who match the name
**similar_to** | Return artists that are similar to the given name
**produced_by** | Return artists who worked with a given producer
**influenced** | Return artists who influenced a given artist
**influenced_by** | Return artists who were influenced by a given artist

### Artist Search Filters

Parameter | Description
-----|------------
**decade** | Return artists who were active during a given decade; [possible values]()
**gender** | Return artists filtered by gender; [possible values]()
**genre** | Return artists filtered by genre; [possible values]()
**influenced** | Return artists who influenced a given artist
**influenced_by** | Return artists who were influenced by a given artist

## Albums

> *Find tracks featured on Michael Jackson's "Thriller" album...*

```shell
$ curl "http://api.musicgraph.com/api/v2/18b44cbd-fa95-e03a-27c6-1fdc02395e63/tracks?fields=id,title&api_key=APIKEY"
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

### Album Metadata Endpoints

Method | Endpoint | Description
-------|----------|------------
`GET` | `/<id>` | Return metadata for an album by id
`GET` | `/<id>/tracks` | List this album's tracks with the `/tracks` connection

### Album Search Endpoint

Method | Endpoint | Description
-------|----------|------------
`GET` | `/album/search` | Execute any number of search operations (described below)

### Album Search Operations

Parameter | Description
-----|------------
**name** | Find albums by name
**artist_name** | Find albums by an artist
**produced_by** | Find albums that were produced by a producer
**similar_to** | Find albums that are similar to another album

### Album Search Filters

Parameter | Description
-----|------------
**country** | Filter album search results by country
**decade** | Filter album search results by release date; [possible values]()
**genre** | Filter album search results by genre; [possible values]()
**genre** | Filter album search results by artist gender; [possible values]()
**top_rated** | Return only top-rated albums; possible values are **true** and **false**

## Tracks

> *Find tracks by Eminem that feature Rihanna as a guest vocalist...*

```shell
$ curl "http://api.musicgraph.com/api/v2/track/search?artist_name=eminem&featuring_artist=rihanna&limit=1&fields=id,title,artist_name,album_title&api_key=APIKEY"
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
$ curl "http://api.musicgraph.com/api/v2/track/search?artist_name=eminem&lyrics_phrase=hailie&limit=5&fields=title&api_key=APIKEY"
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

### Track Metadata Endpoint

Method | Endpoint | Description
-------|----------|------------
`GET` | `/<id>` | Return metadata for a track by id

### Track Search Endpoint

Method | Endpoint | Description
-------|----------|------------
`GET` | `/track/search` | Execute any number of search operations (described below)

### Track Search Operations

Parameter | Description
-----|------------
**name** | Find tracks by name
**artist_name** | Find tracks by an artist
**composed_by** | Find tracks that were composed by an artist
**featuring_artist** | Find tracks that feature an artist

### Track Search Filters

Parameter | Description
-----|------------
**decade** | Filter track search results by release date; [possible values]()
**genre** | Filter track search results by genre; [possible values]()
**lyrics_keywords** | Filter track search results by lyric keywords
**lyrics_lang** | Filter track search results by language
**lyrics_phrase** | Filter track search results that contain an exact match

# Example Endpoints

Description | Endpoint
------------|---------
**artist-metadata** | `/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67?api_key=39ab48c2c0ff9adc16feebf710e43763`
**artist-albums** | `/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67/albums?api_key=39ab48c2c0ff9adc16feebf710e43763`
**artist-tracks** | `/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67/tracks?api_key=39ab48c2c0ff9adc16feebf710e43763`
**artist-metrics** | `/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67/metrics?api_key=39ab48c2c0ff9adc16feebf710e43763`
**artist-metrics-lastfm** | `/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67/metrics/facebook?api_key=39ab48c2c0ff9adc16feebf710e43763`
**artist-metrics-twitter** | `/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67/metrics/twitter?api_key=39ab48c2c0ff9adc16feebf710e43763`
**artist-metrics-vevo** | `/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67/metrics/vevo?api_key=39ab48c2c0ff9adc16feebf710e43763`
**artist-metrics-lastfm** | `/api/v2/artist/ee2564c7-a6b5-11e0-b446-00251188dd67/metrics/lastfm?api_key=39ab48c2c0ff9adc16feebf710e43763`
**album-meta** | `/api/v2/album/f0764c86-be44-bc6c-34a4-c899a1272f02?api_key=39ab48c2c0ff9adc16feebf710e43763`
**album-tracks** | `/api/v2/album/f0764c86-be44-bc6c-34a4-c899a1272f02/tracks?api_key=39ab48c2c0ff9adc16feebf710e43763`
**track-meta** | `/api/v2/track/f05e067b-a6c0-11e0-b446-00251188dd67?api_key=39ab48c2c0ff9adc16feebf710e43763`
**artist-search-by-name** | `/api/v2/artist/search?api_key=39ab48c2c0ff9adc16feebf710e43763&name=madonna`
**artist-similar-to** | `/api/v2/artist/search?api_key=39ab48c2c0ff9adc16feebf710e43763&similar_to=madonna`
**artist-produced-by** | `/api/v2/artist/search?api_key=39ab48c2c0ff9adc16feebf710e43763&produced_by=madonna`
**artist-influenced** | `/api/v2/artist/search?api_key=39ab48c2c0ff9adc16feebf710e43763&influenced=madonna`
**artist-influenced-by** | `/api/v2/artist/search?api_key=39ab48c2c0ff9adc16feebf710e43763&influenced_by=madonna`
**artist-availops-influenced-by** | `/api/v2/artist/availableoperations?api_key=39ab48c2c0ff9adc16feebf710e43763&influenced_by=madonna`
**artist-filter-by-decade** | `/api/v2/artist/search?api_key=39ab48c2c0ff9adc16feebf710e43763&similar_to=madonna&decade=1990s`
**artist-filter-by-genre** | `/api/v2/artist/search?api_key=39ab48c2c0ff9adc16feebf710e43763&similar_to=madonna&genre=Rap`
**artist-filter-by-gender** | `/api/v2/artist/search?api_key=39ab48c2c0ff9adc16feebf710e43763&similar_to=madonna&gender=male`
**artist-availops-filter-by-gender** | `/api/v2/artist/availableoperations?api_key=39ab48c2c0ff9adc16feebf710e43763&similar_to=madonna&gender=male`
**artist-search-by-name-similar** | `/api/v2/artist/search?api_key=39ab48c2c0ff9adc16feebf710e43763&name=madonna&similar_to=bob dylan`
**artist-produced-influenced-by** | `/api/v2/artist/search?api_key=39ab48c2c0ff9adc16feebf710e43763&similar_to=u2&influenced=Coldplay`
**artist-suggest** | `/api/v2/artist/suggest?api_key=39ab48c2c0ff9adc16feebf710e43763&prefix=madon`
**album-by-title** | `/api/v2/album/search?api_key=39ab48c2c0ff9adc16feebf710e43763&name=Houses+Of+The+Holy`
**album-by-artist** | `/api/v2/album/search?api_key=39ab48c2c0ff9adc16feebf710e43763&artist_name=John+Lennon`
**album-similar-to** | `/api/v2/album/search?api_key=39ab48c2c0ff9adc16feebf710e43763&produced_by=Rick+Rubin`
**album-similar-to** | `/api/v2/album/search?api_key=39ab48c2c0ff9adc16feebf710e43763&similar_to=Thriller`
**album-availops-similar-to** | `/api/v2/album/availableoperations?api_key=39ab48c2c0ff9adc16feebf710e43763&similar_to=Thriller`
**album-similar-country** | `/api/v2/album/search?api_key=39ab48c2c0ff9adc16feebf710e43763&similar_to=21&country=UK`
**album-similar-decade** | `/api/v2/album/search?api_key=39ab48c2c0ff9adc16feebf710e43763&similar_to=21&decade=1990s`
**album-similar-genre** | `/api/v2/album/search?api_key=39ab48c2c0ff9adc16feebf710e43763&similar_to=21&genre=Rap`
**album-similar-top-rated** | `/api/v2/album/search?api_key=39ab48c2c0ff9adc16feebf710e43763&similar_to=Thriller&top_rated=true`
**album-availops-similar-top-rated** | `/api/v2/album/availableoperations?api_key=39ab48c2c0ff9adc16feebf710e43763&similar_to=Thriller&top_rated=true`
**track-by-title** | `/api/v2/track/search?api_key=39ab48c2c0ff9adc16feebf710e43763&name=Royals`
**track-artist_name** | `/api/v2/track/search?api_key=39ab48c2c0ff9adc16feebf710e43763&artist_name=Pink`
**track-composed_by** | `/api/v2/track/search?api_key=39ab48c2c0ff9adc16feebf710e43763&composed_by=Sting`
**track-featuring_artist** | `/api/v2/track/search?api_key=39ab48c2c0ff9adc16feebf710e43763&featuring_artist=Rihanna`
**track-availops-featuring_artist** | `/api/v2/track/**availableoperations?api_key=39ab48c2c0ff9adc16feebf710e43763&featuring_artist=Rihanna`
**track-artist-decade** | `/api/v2/track/search?api_key=39ab48c2c0ff9adc16feebf710e43763&artist_name=Lorde&decade=2010s`
**track-inspired-genre** | `/api/v2/track/search?api_key=39ab48c2c0ff9adc16feebf710e43763&artist_name=Shakira&genre=New Age`
**playlist-artist-id** | `/api/v2/playlist?api_key=39ab48c2c0ff9adc16feebf710e43763&artist_id=ee2564c7-a6b5-11e0-b446-00251188dd67`

# Terms And Conditions

We need to protect users, content providers, our software and service while at the same time enabling you to create applications. We therefore require you to comply with the following terms.

1. Do not use the MusicGraph API or its feeds in connection with anything that promotes or takes part in any products, services, or materials that Senzari might consider malicious, hateful, or illegal.

2. You may not sell, lease, share, transfer, or sublicense access to the MusicGraph API or its feeds to any party other than the API key holder.

Please read our [Terms Of Service](https://developer.musicgraph.com/policies "Terms Of Service") before you start developing anything using the MusicGraph API.
