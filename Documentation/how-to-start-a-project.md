# How to start a project

## Prerequisite

Before it makes sense to start a project, please make sure you have the following installed on your machine:

### Vapor

See [Vapor docs](https://docs.vapor.codes) for information on how to set it up. During the beta of Vapor Cloud, you might want to install the beta version of the CLI instead - for more information about this, see the [Vapor Cloud docs](https://docs.vapor.cloud).

### MySQL

For projects using a database, MySQL needs to be installed. This can be done through brew, e.g. `brew install mysql`. See this [guide](https://blog.joefallon.net/2013/10/install-mysql-on-mac-osx-using-homebrew/) for more information.

### Redis

For projects using Redis as a cache, make sure to install this using brew by calling `brew install redis`. See this [guide](https://gist.github.com/nrollr/eb24336b8fb8e7ba5630) for more information.


## Getting started

### Vapor

Our internal template comes with some convenient packages which are used in most of our projects. Remember to look at the documentation for the template for further instructions on how to get it up and running.

```
vapor new my-project-name --template=https://github.com/nodes-vapor/template
```

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
https://github.com/nodes-vapor/storage) package is here to help you:

```swift
import Foundation

try drop.addProvider(StorageProvider.self)
Storage.cdnPathBuilder = { baseURL, path in
    var joinedPath = (baseURL + path)
    joinedPath = joinedPath.replacingOccurrences(of: "/[PROJECT-NAME]/images/original/", with: "/image/[PROJECT-NAME]/")    
    return joinedPath.replacingOccurrences(of: "/[PROJECT-NAME]/data/", with: "/data/[PROJECT-NAME]/")    
}
```
 Remember to change [PROJECT-NAME]
