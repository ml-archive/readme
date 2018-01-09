# How to create Postman API acceptance tests

Writing API acceptance tests is a great way to write system tests while hitting real environments. These tests can be run on your local machine as well as in Vapor Cloud (please consider what environments you're using and why). If it's your first time looking into testing with Postman, then cosider reading through some of these resources:

- [Writing tests in Postman](http://blog.getpostman.com/2017/10/25/writing-tests-in-postman/)
- [API testing tips from a Postman professional](http://blog.getpostman.com/2017/07/28/api-testing-tips-from-a-postman-professional/)
- [JSON Schema](https://spacetelescope.github.io/understanding-json-schema/about.html)
- [Tiny Validator (for v4 JSON Schema)](https://geraintluff.github.io/tv4/)

## Creating a test collection

To keep things separate, then the tests for your API collection should be created in a different collection. If your API collection is named `my-project` then the corresponding test collection should be named `my-project-tests`. Remember to share this collection the same way you would share a normal API collection.

Inside of your test collection, you will then have endpoints for the endpoints you want to test. The order in which you arrange your folders and endpoints (from top to bottom) is **important** since this will be the order in which the Postman Runner will run your tests. Postman Runner is a tool for running all of your tests in a collection.

As with your normal API collection, top level folders should follow the domains of your project:

- Posts
- Authors
- Categories

Depending on the complexity of your project, you might want to create folders inside of these top level folders to describe flows.

Expanding the Posts folder might then reveal the following endpoints in an arranged order:

- Get all posts
- Add post
- Get single post
- Get all posts
- Delete post
- Get single post

## Setting up variables to use for testing

Usually you will want to setup variables that can be used across your tests. These variables could hold the user that's going be used for the tests, the json schemas to use for validation (see "Writing tests" for more information) or something else that might be useful for your tests. We save these variables as "globals" since we can only choose between environment and global variables and since we don't want to clutter our environment variables too much, we'll stick with globals. The variables can be set like this:

```javascript
// Simple variable
pm.globals.set("my-project_tests_email", "postman-test-user@nodes.dk");

// Variable with json
pm.globals.set("my-project_tests_json", JSON.stringify(
	{
      "foo": "bar"
	}
));
```

To retrieve these variables you can do the following:

```javascript
const email = globals.my-project_tests_email
const json = JSON.parse(globals.my-project_tests_json)
```

Note how the variables are being prefixed with the project name followed by `_tests_` to make it clear that the variables are used for testing.

Remember that we're using JavaScript, so we can use JavaScript libraries to help us generate our data. Here's an example of creating a random email for testing an endpoint for creating a user (this is for the purpose of the example since Postman also has a random function):

```javascript
const int = Math.floor(Math.random() * 1000);
const randomEmail = "postman-test-" + int + "@nodes.dk";
```

Since most of these variables will be used throughout the tests, it makes sense to set them up on the first endpoint. Further, to have them available when testing the first endpoint, we can set these variables up in the "Pre-request Script" phase. This means that the variables we set up for testing purposes can be accessed in all endpoints in the body, url and test part of our endpoints.

## Writing tests

### Global tests

Some tests are relevant for all endpoints in our test collection and to avoid having to add them to every single endpoint, these can be setup once and then run automatically for each endpoint in the collection. To do this, edit the collection and hit the "Tests" pane. Here's two examples of tests that could go in here:

```javascript
// Response time tests
pm.test("Response time is less than 2 seconds", function () {
    pm.expect(pm.response.responseTime).to.be.below(2000);
});

// Header tests
pm.test("Content-Type is set correctly", function () {
    pm.response.to.have.header("Content-Type", "application/json; charset=utf-8")
});
```

Our tests will be using a BDD-inspired syntax which you can read more about [here](http://blog.getpostman.com/2017/10/25/writing-tests-in-postman/) (also mentioned at the top).

### Endpoint-specific tests

Coming back to our example on endpoint tests, the first test might be "Get all posts". The tests for this endpoint could look something like:

```Javascripts
// Header tests
pm.test("Response code is set correctly", function () {
    pm.response.to.have.status(200)
});

// Body tests
const paginationSchema = JSON.parse(globals.my-project_tests_schema_pagination);
const postListSchema = JSON.parse(globals.my-project_tests_schema_post_list);
const json = pm.response.json()

pm.test('Pagination schema is valid', function() {
    pm.expect(tv4.validate(json, paginationSchema)).to.be.true;
});

pm.test('Post schema is valid', function() {
    pm.expect(tv4.validate(json.data, postListSchema)).to.be.true;
});
```

There's a couple of things going on here, so let's try and break it down:

#### Testing that the response code is correct

The first test ensures that the response code we're giving back from the endpoint is as we expect. To do this, we can simply fetch the status code from the `pm.response` object.

#### Testing that the response body is correct

Most of the time, we're mostly interested in the structure of the returned JSON and not each individual value. Instead of writing tests that checks to see if each expected key has the expected value, we can use something called [JSON schemas](https://spacetelescope.github.io/understanding-json-schema/about.html) to define how we expect the structure of a response to be and [Tiny Validator](https://geraintluff.github.io/tv4/) to actually validate our schemas.

In order to test using JSON schemas, we first need to define our schemas:

```javascript
const post = {
    "type": "object",
	"properties": {
		"id": {
			"type": "number"
		},
		"userId": {
			"type": "number"
		},
		"title": {
			"type": "string"
		},
		"body": {
			"type": ["string", "null"]
		},
		"createdAt": {
			"type": "string"
		},
		"updatedAt": {
			"type": "string"
		}
	}
};
```

In the above schema, we focus on the properties we expect and the types they should hold. We could do the same to define a schema for how we structure pagination:

```javascript
pm.globals.set("my-project_tests_schema_pagination", JSON.stringify({
    "title": "Pagination",
    "type": "object",
    "properties": {
        "meta": {
            "type": "object",
            "properties": {
            	"paginator": {
            		"type": "object",
            		"properties": {
            			"total": {
            				"type": "number"
            			},
            			"perPage": {
            				"type": "number"
            			},
            			"totalPages": {
            				"type": "number"
            			},
            			"currentPage": {
            				"type": "number"
            			},
            			"queries": {
            				"type": ["string", "null"]
            			},
            			"links": {
            				"type": "object",
            				"properties": {
            					"next": {
            						"type": ["string", "null"]
            					},
            					"previous": {
            						"type": ["string", "null"]
            					}
            				},
            				"minProperties": 2
            			}
            		},
            		"minProperties": 6
            	}
            },
            "minProperties": 1
        },
        "data": {
            "type": "array"
        }
    },
    "minProperties": 2
}));
```

Note how this pagination schema is saved directly as a global variable. We can then use these two pieces to define a schema for a single post and a list of posts (using pagination):

```Javascript
pm.globals.set("my-project_tests_schema_post", JSON.stringify({
	"title": "Post",
	post,
	"minProperties": 12
}));

pm.globals.set("my-project_tests_schema_post_list", JSON.stringify({
    "title": "List of posts",
    "type": "array",
    "items": {
        post
    }
}));
```

It is recommended that schemas are being defined in one place, which should be the "Pre-request Script" phase of your first endpoint test. This will make it easier to find any relevant schema for a test.

## Running your tests

With the tests in place, it's time to actually run them. To run the tests while writing them, we recommend simply requesting the endpoint through Postman. As soon as endpoints should be run all together, the Postman Runner is more convenient. Inside Postman, click "Runner" at the top left corner to bring up a new window with the runner. To start your tests, here's the settings you should consider:

- Collection: This should be your test collection (e.g. `my-project-tests`)
- Environment: The environement you want to run your tests against.
- Iterations: The number of iterations you want to run. 1 should be fine for most projects.
- Delay: The delay between each request. 0 should fine for most projects.
- Log Responses: This tells whether or not Postman Runner should output the request/response of each endpoint in the console.
- Data: If you have a set of data you want to use as variables for your tests, this can be selected.
- Persist Variables: If we were using environment variables in our tests, then this setting would persist them.

With that configured, go ahead and click the "Run my-project-tests". This will start the runner which will go through each endpoint in the collection in the order they are arranged from top to bottom. For each test, the runner will output whether or it succeeded or failed.

