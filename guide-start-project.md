# 1. Vapor
```
vapor new my-project-name --template=https://github.com/nodes-vapor/template
```

# 2. Git
Set up git, add a repository on gitlab and push the initial code

# 3. Redis
Make sure you have Redis installed [Install via Homebrew](https://gist.github.com/nrollr/eb24336b8fb8e7ba5630)

Add dependency
```swift
.Package(url: "https://github.com/vapor/redis-provider.git", majorVersion: 1)
```

```swift
import VaporRedis
import Sessions
try drop.addProvider(VaporRedis.Provider(config: drop.config))
```

Add `redis.json` config
```json
{
    "address": "127.0.0.1",
    "port": "6379",
    "database": 0
}
```

Use Redis cache session middleware instead of build in
Remove the "session" middleware in `droplet.json` and add this in `main.swift`
```swift
import Sessions

drop.middleware.append(SessionsMiddleware(sessions: CacheSessions(cache: drop.cache)))
```

# 4. Crypto
`crypto.json` config - change hash and chipher key (32 bytes) & iv (8 bytes) -> https://www.random.org/strings/
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

# 5. MySQL
Add package
```swift
.Package(url: "https://github.com/vapor/mysql-provider.git", majorVersion: 1, minor: 1),
```

Setup provider
```swift
import VaporMySQL
try drop.addProvider(VaporMySQL.Provider.self)
```

`Config/mysql.json`
```swift
{
    "host": "127.0.0.1",
    "user": "root",
    "password": "secret",
    "database": "PROJECT_NAME",
    "encoding": "utf8mb4"
}
```

# 6. App configuration 
Setup `app.json`

replace `PROJECT_NAME`

```swift
{
    "name": "PROJECT_NAME",
    "url": "0.0.0.0:8080"
}

```

# 7. Akira
Setup `akira.yml`
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

# 8. CORS
Will the API be used by a webapp (in browser, FE team)?
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

# 9. Storage
https://github.com/nodes-vapor/storage
```swift
try drop.addProvider(StorageProvider.self)
Storage.cdnPathBuilder = { baseURL, path in
    var joinedPath = (baseURL + path)
    joinedPath = joinedPath.replacingOccurrences(of: "/[PROJECT-NAME]/images/original/", with: "/image/[PROJECT-NAME]/")    
    return joinedPath.replacingOccurrences(of: "/[PROJECT-NAME]/data/", with: "/data/[PROJECT-NAME]/")    
}
 ```
 Remember to change [PROJECT-NAME]
 
# 10. Gatekeeper - Enforce https in production (Skip for now 23/2-17)
https://github.com/nodes-vapor/gatekeeper
```swift
import Gatekeeper
let enforcerMiddleware = SSLEnforcer(error: Abort.badRequest, drop: drop)
```
Add that middleware to your routes, and make sure to test it!

# 11. Bugsnag
https://github.com/nodes-vapor/bugsnag
```swift
import Bugsnag
let bugsnagMiddleware = try BugsnagMiddleware(drop: drop)
```
