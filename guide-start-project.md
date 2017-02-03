1) `vapor new my-project-name --template=nodes-vapor/template`

2) Set up git, add a repository on gitlab and push the initial code

3) Setup redis

Add dependency
```swift
.Package(url: "https://github.com/vapor/redis-provider.git", majorVersion: 1)
```

```swift
import VaporRedis
import Sessions
try drop.addProvider(VaporRedis.Provider(config: drop.config))
```

Add redis.json config
```json
{
    "address": "127.0.0.1",
    "port": "6379",
    "database": 0
}

```

4) Use redis cache session middleware instead of build in
Remove the "session" middleware in droplet.json and add this in main.swift

```swift
import Sessions

drop.middleware.append(SessionsMiddleware(sessions: CacheSessions(cache: drop.cache)))
```

5)
crypto.json config - change chipher key (32 bytes) & iv (8 bytes) -> https://www.random.org/strings/
```swift
{
    "hash": {
        "method": "sha256",
        "key": "password"
    },
    "cipher": {
        "method": "chacha20",
        "key": "passwordpasswordpasswordpassword",
        "iv": "password"
    }
}
```

6)
Set up MySQL

Add package
```swift
.Package(url: "https://github.com/vapor/mysql-provider.git", majorVersion: 1, minor: 1),
```

Setup provider
```swift
import VaporMySQL
try drop.addProvider(VaporMySQL.Provider.self)
```

config/mysql.json
```swift
{
    "host": "127.0.0.1",
    "user": "root",
    "password": "secret",
    "database": "PROJECT_NAME",
    "encoding": "utf8mb4"
}
```

7)
Setup app.json

replace PROJECT_NAME

```swift
{
    "name": "PROJECT_NAME",
    "url": "0.0.0.0:8080"
}

```

8)
Setup akira.yml
```
project:
    name: CHANGE 
    type: vapor
prepare:
- bugsnag
- mysql
- vapor_config
config:
    database: /Config/mysql.json
docker_template: "vapor-supervisor"
slack_channel: "#CHANGE"
mail:
  - "CHANGE"
```

9)
CORS, will the API be used by a webapp (in browser, FE team)
Luckily this is integrated in Vapor, just add the middleware

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

10) 
Storage
https://github.com/nodes-vapor/storage
```swift
try drop.addProvider(StorageProvider.self)
Storage.cdnPathBuilder = { baseURL, path in
    var joinedPath = (baseURL + path)
    joinedPath = joinedPath.replacingOccurrences(of: "/[PROJECT-NAME]/images/original/", with: "/image/[PROJECT-NAME]/")    
    return joinedPath.replacingOccurrences(of: "/[PROJECT-NAME]/data/", with: "/data/[PROJECT-NAME]/")    
}
 ```
