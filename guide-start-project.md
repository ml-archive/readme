1) `vapor new test --template=https://github.com/nodes-vapor/template`

2) Set up git, add a repository on gitlab and push the initial code

3) Setup redis

Add dependency
```swift
.Package(url: "https://github.com/vapor/redis-provider.git", majorVersion: 1)
```

```swift
import VaporRedis
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
drop.middleware.append(SessionsMiddleware(sessions: CacheSessions(cache: drop.cache)))
```

5)
crypto.json config - change chipher key & iv -> https://www.random.org/strings/
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
Use mysql.json
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
    "name" "PROJECT_NAME",
    "url": "0.0.0.0:8080"
}

```
