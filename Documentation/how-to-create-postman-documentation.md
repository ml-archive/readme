# How to create Postman documentation

Below is an example for how to write Postman documentation. Looking at the raw representation of this file, one should be able to copy/paste this into the documentation for an endpoint. If a section is not relevant for a particular endpoint, please fill in n/a (to make it more clear that it was not just forgotten).

_Please note that each section in this example might not make sense for a real endpoint, but its purpose is just to give guidance on how to fill out each section._

---

Getting a list of users optionally filtered by type.

### Request details

Method: `GET`

URL: `/api/users/:id`

### Headers

`Authorization` (`String`) **required**: Access token (bearer format).

`N-Meta` (`String`) **required**: N-Meta header.

### URL Parameters

`id` (`Int`) **required**: ID for user.

### Query parameters

`lastId` (`Int`): Last ID in the collection of entries.

`limit` (`Int`): Number of entries to return (default: 10).

`typeIds` (`[Int]`): Filtering of types (default: all).

### Parameters

`email` (`String`) **required**: Email for user.

`password` (`String`) **required**: Password for user.

`name` (`String`): User's name.

### Response codes

`200` OK

`401` Unauthorized

`412` Precondition failed
