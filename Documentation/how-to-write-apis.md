# !!! DEPRECATED !!!
These guidelines are no longer in use
Please use [The API Manifesto](https://github.com/monstar-lab-oss/API-manifesto) instead

# How to write APIs

## Table of Contents

- [Introduction](#introduction)
- [Endpoints](#endpoints)
  - [Anatomy of an endpoint](#anatomy-of-an-endpoint)
  - [Request methods](#request-methods)
  - [Examples](#examples)
    - [Tips on what NOT to do](#tips-on-what-not-to-do)
- [Headers](#headers)
- [Images](#images)
  - [A few things to remember](#a-few-things-to-remember)
  - [Examples](#examples-1)
- [Pagination](#pagination)
  - [Using `lastId`](#using-lastid)
    - [Examples](#examples-2)
  - [Using pages](#using-pages)
    - [Examples](#examples-3)
- [Authentication](#authentication)
  - [3rd party authentication](#3rd-party-authentication)
- [HTTP Response Status Codes](#http-response-status-codes)
- [Response](#response)
  - [Errors](#errors)
    - [Vapor 1](#vapor-1)
    - [Vapor 2](#vapor-2)
  - [Examples](#examples-4)
    - [A single item](#a-single-item)
    - [An endpoint without meaningful data to return](#an-endpoint-without-meaningful-data-to-return)
    - [An error in Vapor 2](#an-error-in-vapor-2)
    - [A collection of items](#a-collection-of-items)
    - [A paginated collection of items](#a-paginated-collection-of-items)

## Introduction

This document is a specification of how we are doing (and should do) our internal API’s at Nodes. Our API’s should support usage from our front-end and mobile applications.

If you’re in one of the development departments, consider this to be your personal API bible. If you are ever in doubt about a response code or how to format a URL, look to this document. If it’s not described in here, feel free to make a pull request or reach out to any of the backend developers at Nodes.

We all know that not all projects are the same. So at some point, you might have to bend the rules, use a different convention or even an "incorrect" response code. This is okay, just make sure to go over it with your backend colleagues and inform who is implementing the API of your change.

## Endpoints

Endpoints with literal and readable URLs is what makes an API awesome. So to make everything easy and convenient for you, we have specified how you should do it. No more thinking about if it should be plural or where you should put your slug.

### Anatomy of an endpoint

The anatomy of an endpoint should look like this:

```
/api/{objects}/{slug}/{action}?[filters]&[sorting]&[limit]&[...]
```

Below is a breakdown of the different pieces in the endpoint:

1. All our API’s are prefixed with `/api/`.
2. `{objects}` is the name of the object(s) you are returning. Let’s say you’re retrieving a collection of posts. Then `{objects}` should be `posts`. 
3. `{slug}` can theoretically be anything you’d like. It’s just used as an identifier, usually a unique one. A `{slug}` is used to retrieve a specific item in a collection of objects.
4. `{action}` is used when we want to perform a certain action. This could be, for example, `like`, `follow` or `comment`. The purpose of the action usually depends on the request method.
5. `[filters]` is mostly used when we’re retrieving a collection of objects. There’s no limit to how many filters you can use. All that is required is that they are specified as query parameters.
6. `[sorting]` is exactly like filters. No limit to how many you can use. Just make sure they’re specified as query parameters.
7. `[limit]` is where we specify how many items in a collection we want returned.
8. `[...]` is basically there to tell you that all custom stuff for an endpoint should be specified as parameters.

### Request methods

The request method is the way we distinguish what kind of action our endpoint is being "asked" to perform. For example, `GET` pretty much gives itself. But we also have a few other methods that we use quite often.

| Method   | Description                              |
| -------- | ---------------------------------------- |
| `GET`    | Used to retrieve a single item or a collection of items. |
| `POST`   | Used when creating new items e.g. a new user, post, comment etc. |
| `PATCH`  | Used to update one or more fields on an item e.g. update e-mail of user. |
| `PUT`    | Used to replace a whole item (all fields) with new data. |
| `DELETE` | Used to delete an item.                  |

### Examples

Now that we’ve learned about the anatomy of our endpoints and the different request methods that we should use, it’s time for some examples:

| Method   | URL                                      | Description                              |
| -------- | ---------------------------------------- | ---------------------------------------- |
| `GET`    | `/api/posts`                             | Retrieve all posts.                      |
| `POST`   | `/api/posts`                             | Create a new post.                       |
| `GET`    | `/api/posts/28`                          | Retrieve post #28.                       |
| `PATCH`  | `/api/posts/28`                          | Update data in post #28.                 |
| `POST`   | `/api/posts/28/comments`                 | Add comment to post #28.                 |
| `GET`    | `/api/posts/28/comments?status=approved&limit=10&page=4` | Retrieve page 4 of the comments for post #28 which are approved, with 10 comments per page. |
| `DELETE` | `/api/posts/28/comments/1987` or `/api/comments/1987` | Delete comment #1987.                    |
| `GET`    | `/api/users?active=true&sort=username&direction=asc&search=nodes` | Search for "nodes" in active users, sorted  by username ascendingly. |

#### Tips on what NOT to do

Hopefully you’re already aware of this. But not at any given time should your endpoint end with `.json`, `.xml` or `.something`. If in doubt, reach out to one of the backend developers at Nodes.

## Headers

We love headers! And we have a few that we use pretty much in every one of our endpoints. It’s also a very nice way to send information along with your request without "parameter bombing" your endpoint, e.g. with language or 3rd party tokens.

Below is a list of our most used headers. You might end up working on a project where you have to integrate with this weird 3rd party API, which requires you to make a custom header that is not listed below. Go for it, Batman. No requirements here.

| Header key        | Description                              |
| ----------------- | ---------------------------------------- |
| `Accept`          | This header is **required by all endpoints**. It’s used to identify the request as our own and for versioning our endpoints. **Default value**: `application/vnd.nodes.v1+json`. |
| `Accept-Language` | The [ISO 639](http://www.loc.gov/standards/iso639-2/php/code_list.php) code of language translations should be returned in. |
| `Authorization`   | The authorized user’s token. This is used to gain access to protected endpoint. |
| `N-Meta`          | Meta data about the consumer, such as platform, environment and more, see the [N-Meta package](https://github.com/nodes-vapor/n-meta) |
| `Facebook-Token`  | Facebook Graph token. |
| `Instagram-Token` | Instagram OAuth token. |
| `Twitter-Token`   | Twitter OAuth token. |
| `LinkedIn-Token`  | LinkedIn OAuth token. |
| `Latitude`        | Latitude of a geolocation. This is **only** sent as a header if it’s being used in (almost) every endpoint. |
| `Longitude`       | Longitude of a geolocation. This is **only** sent as a header if it’s being used in (almost) every endpoint. |

## Images

Our APIs will always return the URL to the originally uploaded image. The controlling of dimension, cropping method etc., should be handled by the applications.

The table below shows all available settings you can use on an image. The parameters should be appended to the URL of the image as query parameters.

| Query key  | Type     | Description                              | Extra comments                           |
| ---------- | -------- | ---------------------------------------- | ---------------------------------------- |
| `w`        | `Int`    | Width of image.                          | Optional when `mode` is set to `resize`. |
| `h`        | `Int`    | Height of image.                         | Optional when `mode` is set to `resize` or `fit`. |
| `mode`     | `String` | Available modes: `crop`, `resize` and`fit`. | Defaults to `resize`.                    |
| `download` | `Bool`   | If set to `true` it will force the image to be downloaded. | Defaults to `false`.                     |

### A few things to remember

- When using the mode `resize` and you only provide either width or height, it will resize by the image’s aspect ratio.
- When using the mode `crop` both width and height are **required**. It will always crop from the center of the image and out.
- When using the mode `fit` the image will both be resized **AND** cropped to fit the desired dimensions.
- Images **can not** be upscaled. Meaning that if you request a height of `200` but the image only has a height of `100`, than it will be returned with a height of `100`.

### Examples

Fear not! No table with a list of available options comes without a section with examples of how to use them.

| URL                                | Description                              |
| ---------------------------------- | ---------------------------------------- |
| `/image.jpg?w=500`                 | Set width to `500` and use the height from image’s aspect ratio. |
| `/image.jpg?h=250`                 | Set height to `250` and use the width from the image’s aspect ratio. |
| `/image.jpg?w=100&h=100`           | Resize image to `100x100`.               |
| `/image.jpg?w=250&h=250&mode=crop` | Crop image to `250x250` from the center of image. |
| `/image.jpg?w=300&mode=fit`        | Fit image to a width of `300`.           |
| `/image.jpg?w=150&h=100&mode=fit`  | Fit image to `150x100`.                  |
| `/file.pdf`                        | No parameters makes it viewable.         |
| `/file.pdf?download=true`          | Force download of file.                  |

## Pagination

This is one of the tricky parts. Depending on what type of API you’re doing, pagination is implemented in different ways.

If you're making an API to be used on the web, simple pagination using "pages" would work just fine. This is supported by our [Paginator package](https://github.com/nodes-vapor/paginator) and should work out of the box.

But if you’re making an API for the mobile team(s) then you need to do it in a bit more complex way. Because devices usually have a "load more" feature, we can’t use the "pages" approach, since we could risk getting duplicates or even miss new entries. Therefore we return the collection in "batches" instead of pages.

See the Response section for examples of how to return meta data for pagination.

### Using `lastId`

Let’s assume we want to have a feed of posts. 10 posts per "load more". Every time our endpoint is being requested we return a collection of 10 posts. When a user then scrolls to the bottom of the feed, they trigger the "load more" feature. The mobile app then requests the same endpoint but with a `lastId` query parameter.

This parameter contains the ID of the last item in the previous returned batch of posts. When we receive the `lastId` parameter, we use that ID to only return posts that have IDs which are greater than the received `lastId`.

Please note that in order for this approach to work, the collection needs to be sorted by ID.

#### Examples

It might sound a bit complicated, but it’s really not. Here is a few examples, which hopefully makes it a bit more understandable. Otherwise just ask one of your co-workers.

| Endpoint                | Description                              |
| ----------------------- | ---------------------------------------- |
| `/api/posts`            | Initial request. Return first 10 posts.  |
| `/api/posts/?lastId=10` | 1st time we "load more". Return 10 posts starting from ID #11. |
| `/api/posts/?lastId=20` | 2nd time we "load more". Return 10 posts starting from ID #21. |
| `/api/posts/?lastId=30` | 3rd time we "load more". Return 10 posts starting from ID #31. |

### Using pages

If one decides that a "pages" approach works for their API, the `Paginator` package can be used. This package takes care of paginating the results as well as building the meta data for the response.

#### Examples

| Endpoint             | Description                              |
| -------------------- | ---------------------------------------- |
| `/api/posts`         | Initial request. Return first 10 posts.  |
| `/api/posts/?page=2` | Second page, returning 10 posts using an offset of 10. |
| `/api/posts/?page=3` | Third page, returning 10 posts using an offset of 20. |

## Authentication

One of the most essential parts of an API is authentication. This is why authentication is also one of the most important parts - security wise - when you’re making an API. Therefore we’ve set up a few guidelines, which you should follow at any time. Most of them are pretty obvious, but nonetheless it’s better to be safe than sorry.

- **Always** use a SSL-encrypted connection when trying to authenticate an user.
- **Always** save passwords hashed/encrypted. Never save passwords as plain text.
- **Never** save 3rd party tokens (i.e. Facebook, Twitter or Instagram).
- The **only time** you should ever return an user’s API token is when a user either is **successfully created** or **successfully authenticated**.

### 3rd party authentication

In some projects we might have to give a user the option to authenticate with e.g. their Facebook account. To support this, you need to have a column on your user where you can save the user’s Facebook ID.

Then you make an endpoint, which requires a valid Facebook token. You extract the Facebook ID from the received token and look for an entry with that Facebook ID in your database. If found, you authenticate the found user. If not found, either create the user or return invalid login, depending on the project specification.

Not at any given time should you perform token authentication. The department implementing your API should always have done that before requesting your endpoint. If they don’t send a valid token, you should return an error and not continue with your authentication.

## HTTP Response Status Codes

One of the most important things in an API is how it returns response codes. Each response code means a different thing and consumers of your API rely heavily on these codes.

| Code  | Title                     | Description                              |
| ----- | ------------------------- | ---------------------------------------- |
| `200` | `OK`                      | When a request was successfully processed (e.g. when using `GET`, `PATCH`, `PUT` or `DELETE`). |
| `201` | `Created`                 | Every time a record has been added to the database (e.g. when creating a new user or post). |
| `304` | `Not modified`            | When returning a cached response. |
| `400` | `Bad request`             | When the request could not be understood (e.g. invalid syntax). |
| `401` | `Unauthorized`            | When authentication failed. |
| `403` | `Forbidden`               | When an authenticated user is trying to perform an action, which he/she does not have permission to. |
| `404` | `Not found`               | When URL or entity is not found. |
| `440` | `No accept header`        | When the required "Accept" header is missing from the request. |
| `422` | `Unprocessable entity`    | Whenever there is something wrong with the request (e.g. missing parameters, validation errors) even though the syntax is correct (ie. `400` is not warranted). |
| `500` | `Internal server error`   | When an internal error has happened (e.g. when trying to add/update records in the database fails). |
| `502` | `Bad Gateway`             | When a necessary third party service is down. |

The response codes often have very precise definition and are easily misunderstood when just looking at their names. For example, `Bad Request` refers to malformed requests and not, as often interpreted, when there is something semantically wrong with the reuquest. Often `Unprocessable entity` is a better choice in those cases.
Another one that is often used incorrectly is `Precondition Failed`. The precondition this status code refers to are those defined in headers like `If-Match` and `If-Modified-Since`. Again, `Unprocessable entity` is usually the more appropriate choice if the request somehow isn't valid in the current state of the server.
When in doubt, refer to [this overview](https://httpstatuses.com) and see if the description of an status code matches your situation.

## Response

Generally we have a few rules the response has to follow:

- Root should always be returned as an object.
- Keys should always be returned as camelCase.
- When we don’t have any data, we need to return in the following way:
  - Collection: Return empty array.
  - Empty key: Return null or unset the key.
- Consistency of key types. e.g. always return IDs as an integer in all endpoints.
- Date/timestamps should always be returned with a time zone.
- Content (being a single object or a collection) should be returned in a key (e.g. `data`).
- Pagination data should be returned in a `meta` key.
- Endpoints should always return a JSON payload.
  - When an endpoint doesn't have meaningful data to return (e.g. when deleting something), use a `status` key to communicate the status of the endpoint.

### Errors

When errors occur the consumer will get a JSON payload verifying that an error occurred together with a reason for why the error occurred. 

Error handling has changed from Vapor 1 through 3, these are the keys to expect from the different versions.

#### Vapor 3

| Endpoint   | Description                              |
| ---------- | ---------------------------------------- |
| `error`    | A boolean confirming an error occurred.  |
| `reason`   | A description of the error that occurred. For some errors this value provides extra information on non-production environments. |

#### Vapor 2

| Endpoint   | Description                              |
| ---------- | ---------------------------------------- |
| `error`    | A boolean confirming an error occurred.  |
| `reason`   | A description of the error that occurred. |
| `metadata` | Any custom metadata that might be included. **Only** available on a non-production environment. |

#### Vapor 1

| Key        | Description                              |
| ---------- | ---------------------------------------- |
| `code`     | The HTTP code.                           |
| `error`    | A boolean confirming an error occurred.  |
| `message`  | A description of the error that occurred. |
| `metadata` | Any custom metadata that might be included. **Only** available on a non-production environment. |

### Examples

Just to round it all off, here’s a few examples of how our response will return depending on whether you’re about to return a single item, a collection or a paginated result set.

#### A single item

```
{
    "data": {
        "id": 1,
        "name": "Shane Berry",
        "email": "shane@berry.com"
        "created_at": "2015-03-02T12:59:02+0100",
        "updated_at": "2015-03-04T15:50:40+0100"
    }
}
```

#### An endpoint without meaningful data to return

```
{
    "status": "ok"
}
```

#### An error in Vapor 2 or 3

```
{
    "error": true,
    "reason": "Invalid email or password"
}
```

#### A collection of items

```
{
    "data": [
        {
            "id": 1,
            "name": "Shane Berry",
            "email": "shane@berry.com"
            "created_at": "2015-03-02T12:59:02+0100",
            "updated_at": "2015-03-04T15:50:40+0100"
        },
        {
            "id": 2,
            "name": "Albert Henderson",
            "email": "albert@henderson.com"
            "created_at": "2015-03-02T12:59:02+0100",
            "updated_at": "2015-03-04T15:50:40+0100"
        },
        {
            "id": 3,
            "name": "Miguel Phillips",
            "email": "miguel@phillips.com"
            "created_at": "2015-03-02T12:59:02+0100",
            "updated_at": "2015-03-04T15:50:40+0100"
        }
    ]
}
```

#### A paginated collection of items
```
{
    "data": [
        {
            "id": 1,
            "name": "Shane Berry",
            "email": "shane@berry.com"
            "created_at": "2015-03-02T12:59:02+0100",
            "updated_at": "2015-03-04T15:50:40+0100"
        },
        {
            "id": 4,
            "name": "Albert Henderson",
            "email": "albert@henderson.com"
            "created_at": "2015-03-02T12:59:02+0100",
            "updated_at": "2015-03-04T15:50:40+0100"
        }
    ],
    "meta": {
        "pagination": {
            "currentPage": 2,
            "links": {
                "next": "/api/users/?page=3&count=20",
                "previous": "/api/users/?page=1&count=20"
            },
            "perPage": 20,
            "total": 258,
            "totalPages": 13
        }
    }
}
```
