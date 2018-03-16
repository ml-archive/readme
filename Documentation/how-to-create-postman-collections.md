# How to create Postman collections

We use [Postman](https://www.getpostman.com/) heavily at Nodes for tasks like:

- Requesting our internal and external APIs to see if they behave as expected
- Documenting our APIs
- Writing tests for our APIs
- Mocking our APIs
- Setting up monitoring for our APIs

To align how we work with Postman, this document was created.

## How to setup a new collection

### Creating a collection

Each project at Nodes that contains an API should have a corresponding Postman collection. A collection can be considered as a project or simply a folder in Postman. The collection should have the same name as  the project on GitHub but without the framework suffix. See [this document](https://github.com/nodes-projects/readme/blob/master/general/new-repository.md) for more info on our repo naming convention (internal link).

### Sharing a collection

With your new collection created, the next step is to make sure the rest of your team is able to see it. To do this, you're going to click the "…" icon on your new collection and click "Share Collection". This will bring up a new dialogue where you're able to specify how to share the collection:

- Team: Can View
- Individual permissions for each backend developer at Nodes: Can Edit
- QA Lead: Can Edit

This will allow consumers of the API to read about it as well as trying it out. It will also allow any other backend developer who might work on the project in the future to edit the collection if needed. Remember that you need to be invited to the Nodes team on Postman before you can publish it to the team.

### Structure of a collection

At the top level, the folders created should follow the different domains of the available endpoints. These domains will most likely follow the different models and controllers in your project. As an example, let's consider an API for interacting with a blog. At the top level, the collection could have the following folders:

- Posts
- Authors
- Categories

Sometimes, it makes sense to have nested folders to group related endpoints within a specific domain. In the above example, that could be categories for a specific post (e..g `/posts/:id/categories`). In that case, it's fine to have another folder inside Posts that group the endpoints related to categories for a specific post. Remember to keep the top level folder for Categories for any endpoints that are not tied to a specific post.

Endpoints for the different domains should then be created within each top level domain folder. The endpoints should be named to describe their action. Having a reference to the domain in the name of the endpoint can be convenient when having multiple tabs open in the Postman application. 

Expanding the Posts folder could then have endpoints like:

- All posts
- Specific post
- Create post
- Update post
- Delete post

In some cases, it might be convenient to setup endpoints for dealing with a third party API. To keep things separate and to not confuse any consumers of our APIs, a new collection should be created using the same collection name but with a "-backend" suffix to indicate that this collection is to be used by the backend team only.

## How to setup variables

To make it easier to move between different environments when testing our APIs we use Postman variables. It's important to know the difference between **environment variables** and **collection variables**:

### Environment variables

Environment variables are the ones that specify environment-specific values, e.g. how we are hosting our projects. Since we use Vapor Cloud for all of our projects and since we always use a development, staging and production environment, three templates for these three setups has been made:

- Vapor Cloud Development

- Vapor Cloud Staging

- Vapor Cloud Production

  Each template will come with the same set of variables but with different values. Here's the list of variables the templates will contain:

- `scheme` (e.g. `https://`)

- `host` (e.g. `.vapor.cloud`)

- `baseURL` (`{{scheme}}://{{appName}}{{host}}:{{port}}`)

- `port` (e.g. `443`)

- `accessToken` (`{{{{appName}}-accessToken}}`)

- `refreshToken` (`{{{{appName}}-refreshToken}}`)

Note how the `baseURL` is being constructed using other environment variables and using a collection variable. `accessToken` and `refreshToken` are also being constructed using a collection variable called `appName` which will be explained below.

These environments can be found under the "Team Library" and by looking at the "Environment Templates". We suggest that you add these three templates to your setup and if relevant, feel free to go ahead and add your own local environment if you're running the project on your machine. The only requirement for your local environment is to have a variable called `baseURL` (e.g. `http://0.0.0.0:8080`), one called `refreshToken` and one called `accessToken`. `accessToken` and `refreshToken`  should just have the same values as the ones coming from any of the templates.

### Collection variables

Collection variables should be all the values that is relevant for your collection and which are not depending on the selected environment. Currently there's one required variable which needs to be set for each collection:

- `appName` (e.g. `my-blog`)

This variable should hold the name of the application name on Vapor Cloud. If the URL for your application on Vapor Cloud is `https://my-blog.vapor.cloud` then `my-blog`  is your `appName` value. Feel free to add any other relevant collection variables when needed.

### Working with access tokens

Most of our APIs will have endpoints which requires a token (either a access token or a refresh token). This could be protected endpoints where a user is required to authenticate before giving access. To make it easier to move between projects and environments, the following code should be added under "Tests" for the relevant endpoints where tokens are being returned:

```javascript
let json = pm.response.json();
let accessTokenKey = pm.variables.get("appName") + "-accessToken"
let refreshTokenKey = pm.variables.get("appName") + "-refreshToken"

pm.environment.set(accessTokenKey, json.accessToken)
pm.environment.set(refreshTokenKey, json.refreshToken)
```

This code snippet expects an `accessToken` and a `refreshToken` at the top level of the JSON response of the endpoint. Endpoints which logins a user or creates a user are good examples of endpoints where this might be relevant. Remember to align the code to with what tokens you are expecting (some projects might not be using refresh tokens).

## How to create endpoint documentation

Postman documentation is covered in its own file [here](https://github.com/nodes-vapor/readme/blob/master/Documentation/how-to-create-postman-documentation.md).

## How to create API acceptance tests

Postman API acceptance testing is convered in its own file [here](https://github.com/nodes-vapor/readme/blob/master/Documentation/how-to-create-postman-tests.md).