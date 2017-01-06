1) vapor new project name

2) Set up git, add a repository on gitlab and push the initial code

3) Setup bugsnag, by creating a bugsnag application and https://github.com/nodes-vapor/bugsnag

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

5) clients.json change mozzilla with defaults
```swift
"certificates": "defaults"
```
