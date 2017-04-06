# Best Practice APIs

## Introduction

This document is a specification of how we are doing (and should do) our internal API’s at Nodes. Our API’s should support usage from our front-end and mobile applications.

If you’re in one of the development departments, consider this to be your personal API bible. If you are ever in doubt about a response code or how to format a URL, look to this document. If it’s not described in here, feel free to make a pull request or reach out to any of the backend developers at Nodes.

We all know that not all projects are the same. So at some point, you might have to bend the rules, use a different convention or even an “incorrect” response code. This is okay, just make sure to go over it with your backend colleagues and inform who is implementing the API of your change.

## Endpoints

Endpoints with literal and readable URLs is what makes an API awesome. So to make everything easy and convenient for you, we have specified how you should do it. No more thinking about if it should be plural or where you should put your slug. Yay!


### Anatomy of an endpoint

The anatomy of an endpoint should look like this:

```
/api/{objects}/{slug}/{action}/?[filters]&[sorting]&[limit]&[...]
```

Below is a breakdown of the different pieces in the endpoint:

1. All our API’s are prefixed with `/api/`.
2. `{objects}` is the name of the object(s) you are returning. Let’s say you’re retrieving a collection of posts. Then `{objects}` should be `posts`. 
3. `{slug}` can theoretically be anything you’d like. It’s just used as an identifier. `9` out of `10` times this is usually a unique identifier. A `{slug}` is used to retrieve a specific item in a collection of objects.
4. `{action}` is used when we want to perform a certain action. This could be, for example, like, follow or comment. The purpose of the action usually depends on the request method.
5. `[filters]` is mostly used when we’re retrieving a collection of objects. There’s no limit to how many filters you can do. All that is required is that they are specified as query parameters.
6. `[sorting]` is exactly like filters. No limit to how many you can do. Just make sure they’re specified as query parameters.
7. `[limit]` is where we specify how many items in a collection we want returned.
8. `[...]` is basically there to tell you that all custom stuff for an endpoint should be specified as parameters.


### Request methods

The request method is the way we distinguish what kind of action our endpoint is being “asked” to perform. For example, `GET` pretty much gives itself. But we also have a few other methods that we use quite often.

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
| `POST`   | `/api/posts/comments`                    | Add comment to post #28.                 |
| `GET`    | `/api/posts/comments/?status=approved&limit=10&page=4` | Retrieve comments for post #28 which are approved. But only 10 per page - starting from page 4. |
| `DELETE` | `/api/comment/1987`                      | Delete comment #1987.                    |
| `GET`    | `/api/users/?active=true&sort=username&direction=asc&search=nodes` | Search for “nodes” in active users, sorted  by username ascendingly. |


#### Tips on what NOT to do

Hopefully you’re already aware of this. But not at any given time should your endpoint end with `.json`, `.xml` or `.something`. If in doubt, reach out to any of the backend developers at Nodes.


## Headers

We love headers! And we have a few that we use pretty much in every one of our endpoints. It’s also a very nice way to send information along with your request without “parameter bomb” your endpoint, e.g. language or 3rd party tokens.

Below is a list of our most used headers. You might end up working on a project where you have to integrate with this weird 3rd party API, which requires you to make a custom header that is not listed below. Go for it, Batman. No requirements here.

| Header key        | Description                              |
| ----------------- | ---------------------------------------- |
| `Accept`          | This header is **required by all endpoints**. It’s used to identify the request as our own and to versioning our endpoints. **Default value**: `application/vnd.nodes.v1+json`. |
| `Authorization`   | Should contain the authorized user’s token. This is used to gain access to protected endpoint. |
| `Language`        | Should contain ISO 639 code of language translations should be returned in. |
| `Facebook-Token`  | Should contain a valid Facebook Graph token. |
| `Instagram-Token` | Should contain a valid Instagram OAuth token. |
| `Twitter-Token`   | Should contain a valid Twitter OAuth token. |
| `LinkedIn-Token`  | Should contain a valid LinkedIn OAuth token. |
| `Latitude`        | Should contain latitude of a geolocation. This is **only** sent as a header if it’s being used in (almost) every endpoint. |
| `Longitude`       | Should contain longitude of a geolocation. This is **only** sent as a header if it’s being used in (almost) every endpoint. |


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

- When using the mode **resize** and you only provide either width or height, it will resize by the image’s aspect ratio.
- When using the mode **crop** both width and height are **required**. It will always crop from the center of the image and out.
- When using the mode **fit** the image will both be resized **AND** cropped to fit the desired dimensions.
- Images **can not** be upscaled. Meaning that If you request a height of `200` but the image only has a height of 100, than it will be returned with a height of `100`.

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

TODO UPDATE THIS

This is one of the tricky parts. Depending on what type of API you’re doing, pagination is implemented in different ways.

If you're making an API to be used on the web, simple pagination using "pages" would work just fine. This is supported by our Paginator package and should work out of the box.

But if you’re making an API for the mobile team(s) then you need to do it in a bit more complex way. Because devices usually have a “load more” feature, we can’t use the shipped pagination, since we could risk getting duplicates or even miss new entries. Therefore we return the collection in “batches” instead of pages.

### Using `lastId`

TODO

#### Examples

It might sound a bit complicated, but it’s really not. Here is a few examples, which hopefully makes it a bit more understandable. Otherwise just ask one of your co-workers.

| Endpoint                | Description                              |
| ----------------------- | ---------------------------------------- |
| `/api/posts`            | Initial request. Return first 10 posts.  |
| `/api/posts/?lastId=10` | 1st time we “load more”. Return 10 posts starting from ID #11. |
| `/api/posts/?lastId=20` | 2nd time we “load more”. Return 10 posts starting from ID #21. |
| `/api/posts/?lastId=30` | 3rd time we “load more”. Return 10 posts starting from ID #31. |


### Using pages

TODO

#### Examples

TODO


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


## Response codes

One of the most important things in an API is how it returns response codes. Each response code means a different thing and consumers of your API rely heavily on these codes.

| Code  | Title                     | Description                              |
| ----- | ------------------------- | ---------------------------------------- |
| `200` | `OK`                      | Should be returned when a request was successfully processed. e.g. when using `GET`, `PATCH`, `PUT` or `DELETE`. |
| `200` | `Created`                 | Should be returned every time a record has been added to the database. e.g. when creating a new user or post. |
| `304` | `Not modified`            | Should be returned when returning a cached response. |
| `401` | `Unauthorized`            | Should be returned when authentication failed. |
| `403` | `Forbidden`               | Should be returned when an authenticated user is trying to perform an action, which he/her does not permission to. |
| `404` | `Not found`               | Should be returned when URL is not found. e.g. when route is invalid. And NOT when an entity was not found. |
| `412` | `Precondition failed`     | Should be returned when validation has failed. Error message should contain which validation rules has failed. |
| `440` | `No accept header`        | Should be returned when the required “Accept” header is missing from the request. |
| `441` | `No token was provided`   | Should be returned when a user is trying to request a protected endpoint without a token. |
| `442` | `Invalid token`           | Should be returned when a user is trying to request a protected endpoint with an invalid token. |
| `443` | `Token has expired`       | Should be returned when a user is trying to request a protected endpoint with an expired token. |
| `444` | `Invalid 3rd party token` | Should be returned when a received 3rd party token is either invalid or expired. |
| `445` | `Entity not found`        | Should be returned when an entity wasn’t found. e.g. when requesting User #28 and that user has been deleted. |
| `446` | `Unverified`              | User is not verified.                    |
| `500` | `Internal server error`   | Should be returned when an internal error has happened.e.g. when trying to add/update records in the database fails. |
| `550` | `Save failed`             | Should be returned when an entity was not successfully saved to the database. Inserts, updates and deletes. |

## Response

Generally we have a few rules the response has to follow:

- Root should always be returned as an object.
- Keys should always be returned as camelCase.
- When we don’t have any data, we need to return in the following way:
  - Collection: Return empty array.
  - Empty key: Return null or unset the key.
- Consistency of key types. e.g. always return IDs as an integer in all endpoints.
- Date/timestamps should always be returned with a time zone.
- Collections should be returned in a key (e.g. `data`).
- Pagination data should be returned in a `meta` key.

### Examples

Just to round it all off, here’s a few examples of how our response will return depending on whether you’re about to return a single item, a collection or a paginated result set.

#### A single item

```
{
    "id": 1,
    "name": "Shane Berry",
    "email": "shane@berry.com"
    "created_at": "2015-03-02T12:59:02+0100",
    "updated_at": "2015-03-04T15:50:40+0100"
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

