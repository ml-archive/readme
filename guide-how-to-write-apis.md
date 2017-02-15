# Best Practice APIs

## Postman

### Search dialogue tree

Endpoint for fetching the entire dialogue tree

#### Request details

Method: `GET`

URL: `/api/dialogue`

#### Headers

`Accept` (string) **required**

`Authorization` (string) **required** *access token*

#### Parameters

`node_id` (integer) **required** *Id of of last node user encountered (cannot have any children)*

`image` (file) *File upload for image type nodes only*

`text_answer` (string) *Free text answer for text type nodes only*

#### Query parameters

`lastId` (integer) *Last ID in the collection of entries*

`limit` (integer) *Number of entries to return (default: 10)*

`typeIds` *(comma seperated ids) (default: all)*

`municipalityIds` *(comma seperated ids) (default: all)*

#### URL parameter(s)

`:warning_id` (integer) *ID of warning*

#### Available includes

n/a

#### Response codes(s)

`200` OK

`412` Precondition failed





- How to version APIs
- Folder structure guide