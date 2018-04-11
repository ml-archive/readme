# How to start a project

## Prerequisites

Before it makes sense to start a project, please make sure you have the following installed on your machine:

### Vapor

See [Vapor docs](https://docs.vapor.codes) for information on how to set it up. During the beta of Vapor Cloud, you might want to install the beta version of the CLI instead - for more information about this, see the [Vapor Cloud docs](https://docs.vapor.cloud).

### MySQL

For projects using a database, MySQL needs to be installed. This can be done through brew, e.g. `brew install mysql`. See this [guide](https://blog.joefallon.net/2013/10/install-mysql-on-mac-osx-using-homebrew/) for more information.

### Redis

For projects using Redis as a cache, make sure to install this using brew by calling `brew install redis`. See this [guide](https://gist.github.com/nrollr/eb24336b8fb8e7ba5630) for more information.


## Getting started

### Vapor

Our Vapor template comes with some convenient packages which are used in most of our projects. Remember to look at the [documentation](https://github.com/nodes-vapor/template/blob/master/README.md) for the template for further instructions on how to get it up and running.

```
vapor new my-project-name --template=https://github.com/nodes-vapor/template
```

### GitHub

#### Creating the repo

After you've aligned the project name with your project team, you can create the repo on the nodes-projects organization. Check this [guide](https://github.com/nodes-projects/readme/blob/master/general/new-repository.md) (internal) for information on naming of repos.

#### `LICENSE`

If you're setting up a customer project, then go ahead and delete the `LICENSE` file. If it's an open source project, then keep the MIT license file but make sure to update any year references if needed.

#### `README`

Have a look at this [template](https://github.com/nodes-projects/readme/blob/master/general/readme-template.md) (internal) for setting up your `README` file for customer projects. Towards the top of the file, make sure to add the relevant badges. It should look something like:

```
[Swift version] [Vapor version]
`master`: [Circle CI] [Codebeat]
`develop`: [Circle CI] [Codebeat]
```

See some of the other projects on the nodes-projects organization for comparison and check out the Circle CI and Codebeat sections below for information on how to generate the badges.

#### Branches

After the repository has been created and Circle CI (see [Circle CI](#circle-ci) section) has been setup, go to the "Settings" part of the repository and hit "Branches". In here you want to make sure that `develop` is set as the default branch and that `master` and `develop` are added as protected branches. For each protected branch make sure that the following options are checked:

- [x] "Protect this branch"
- [x] "Require status checks to pass before merging"
- [x] "Require branches to be up to date before merging"
- [x] "ci/circleci: Linux"
- [x] "ci/circleci: MacOS" (if running Vapor 2 or newer)

### Circle CI

After the repository has been created on GitHub, the project can be added to Circle CI. Make sure you're on the nodes-projects organization (on Circle CI), hit "Add Projects". On the following page search for your repository name and hit "Set Up Project". On the next page choose "Linux" under "Operating "System" and hit "Start building" at the bottom of the site.

Last step is to generate a token for the Circle CI badge for the readme. Select your newly created project on Circle CI and hit settings (the gear icon), go to "API Permissions" and hit "Create Token". Choose "Status" as scope and write "Badge" as the token label. This generated token can now then be used for the Circle CI badge for the readme by inserting it into the url at the right spot:

```
[![Circle CI](https://circleci.com/gh/nodes-projects/my-project/tree/master.svg?style=shield&circle-token=my-token)](https://circleci.com/gh/nodes-projects/my-project)
```

### Codebeat

We use Codebeat for static code analysis. Although the reports and the GPA score should be taken with a grain of salt, the reports are still useful for getting recommendations for areas to improve in the codebase. Each repository and branch needs to be setup manually. After logging into Codebeat (please reach out to a fellow developer for credentials) you can add the `master` and `develop` branches for the repository. When Codebeat is done analyzing, the badges for the readme will be available under "Settings" within the different reports.

### Deployment

Using the Vapor Cloud CLI, your Vapor project can be deployed to the cloud. See the [Vapor Cloud docs](https://docs.vapor.cloud/) for more information on how to do this.

## Optional packages

### CORS
If the API is going to be used in a web app, consider adding the [CORS middleware](https://docs.vapor.codes/2.0/http/cors/#cors).

```
let corsConfiguration = CORSConfiguration(
    allowedOrigin: .all,
    allowedMethods: [.get, .post, .put, .options, .delete, .patch],
    allowedHeaders: ["Accept", "Authorization", "Content-Type", "Origin", "X-Requested-With", "N-Meta"]
)
drop.middleware.insert(CORSMiddleware(configuration: corsConfiguration), at: 0)
```
Read more: https://github.com/vapor/documentation/blob/master/http/cors.md

Test: http://codepen.io/dennishn/pen/BLbYyJ


### Storage

If you need to store files, e.g. images, the [Storage](
https://github.com/nodes-vapor/storage) package is here to help you.
