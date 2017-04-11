### Kill vapor process, fx after xcode crash
Asking for processes on port `8080` and then kill the `PID`:
```
-> lsof -i :8080
COMMAND   PID     USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME
App     38017 casper_r    4u  IPv4 0xb334c0f620b8c201      0t0  TCP *:http-alt (LISTEN)

-> kill 38017
```

### Retrieve client ip
```swift
try request.peerAddress?.address()
```

### URLs in `Package.swift`
Make sure that all URLs for packages in `Package.swift` uses `https` and ends with `.git`.
