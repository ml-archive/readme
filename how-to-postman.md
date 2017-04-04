# Postman

Below is an example for how to write Postman documentation. Looking at the raw representation of this file, one should be able to copy/paste this into the documentation for an endpoint.

_Please note that each section in this example might not make sense for a real endpoint, but its purpose is just to give guidance on how to fill out each section._

## Search users

Endpoint for fetching the entire dialogue tree

### Request details

Method: `GET`

URL: `/api/users`

### Headers

`Authorization` (`String`) **required**: Access token (bearer format).

`N-Meta` **required**: N-Meta header.

### Parameters

``email` (`String`) **required**: Email for user.

`password` (`String`) **required**: Password for user.

`firstName` (`String`): User's first name.*

### Query parameters

`lastId` (`Int`): Last ID in the collection of entries.

`limit` (`Int`): Number of entries to return (default: 10).

`typeIds` *(`[Int]`): Filtering of types (default: all).

### Response codes

`200` OK

`401` Unauthorized

`412` Precondition failed