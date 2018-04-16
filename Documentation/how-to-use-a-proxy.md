# How to use a proxy

In some cases you might need to use a proxy for performing requests in your Vapor client, for example if a certain IP is whitelisted when reaching a third party integration. It's not straightforward to set this up and hopefully in the future, this will be a bit more simple and aligned.

## Without TLS (using `http`)

If there's no requirements for using `https`, a proxy can be defined in Vapor 2 as described in the [official docs](https://docs.vapor.codes/2.0/http/client/#proxy).

## With TLS (using `https`)

Using a proxy with requirements for using `https` becomes a bit more challenging. First, we suggest that you set your client to `foundation` in your `droplet.json` since this client supports TLS.

### macOS

Since we're using the `FoundationClient` we can use the provided support for proxying:

```swift
let configuration = URLSessionConfiguration.default

let hostname = "00.000.000.000" // my proxy IP
let port = 8888 // my proxy port

configuration.connectionProxyDictionary = [
    kCFNetworkProxiesHTTPEnable: true,
    kCFNetworkProxiesHTTPProxy: hostname,
    kCFNetworkProxiesHTTPPort: port,
    kCFNetworkProxiesHTTPSEnable: true,
    kCFNetworkProxiesHTTPSProxy: hostname,
    kCFNetworkProxiesHTTPSPort: port
]

HTTP.FoundationClient(
    scheme: "https",
    hostname: "www.google.com",
    port: 443,
    session: URLSession(configuration: configuration)
)
```

The above code will then give you a `FoundationClient` that will proxy all reguests through `00.000.000.000:8888`.

### Linux

Since we cannot define the `connectionProxyDictionary` on Linux, we unfortunately cannot use the above approach. Hopefully this will be ported in the future, but until then, we discovered a minor hack we can use in the meantime.

If you set the `HTTPS_PROXY` environment variable to your hostname and port, then `FoundationClient` which depends on `cURL`, will use that variable for proxying your requests. Following the macOS example you could set it to: `00.000.000.000:8888` to use that IP and port for proxying your requests.

This might break in the future, but at this moment, this is the only way we're discovered you can proxy using TLS without having to roll out your own client on top of `cURL`.

Consider using the OS conditionals (e.g. `if os(macOS)`) for using both approaches, so that the proxy works when developing locally on macOS and when deploying to e.g. Vapor Cloud.