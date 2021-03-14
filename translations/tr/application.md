---
description: Uygulama örneği geleneksel olarak Fiber uygulamasına delalet eder.
---

# 🚀 Application

## New

Bu metot örnek isimli yeni bir **uygulama** oluşturur. Yeni bir örnek oluştururken isteğe bağlı [ayarları](application.md#settings) verebilirsiniz.

{% code title="Signature" %}
```go
fiber.New(settings ...*Settings) *App
```
{% endcode %}

{% code title="Example" %}
```go
package main

import "github.com/gofiber/fiber"

func main() {
    app := fiber.New()

    // ...

    app.Listen(3000)
}
```
{% endcode %}

## Settings

`New`'i çağırırken isteğe bağlı ayarları geçebilirsiniz.

{% code title="Example" %}
```go
func main() {
    // Yeni bir örneği yaratan ayarları geç
    app := fiber.New(&fiber.Settings{
        Prefork:       true,
        CaseSensitive: true,
        StrictRouting: true,
        ServerHeader:  "Fiber",
    })

    // ...

    app.Listen(3000)
}
```
{% endcode %}

Veya bir `uygulama` başlattıktan sonra ayarları değiştir.

{% code title="Example" %}
```go
func main() {
    app := fiber.New()

    // Or change Settings after creating an instance
    app.Settings.Prefork = true
    app.Settings.CaseSensitive = true
    app.Settings.StrictRouting = true
    app.Settings.ServerHeader = "Fiber"

    // ...

    app.Listen(3000)
}
```
{% endcode %}

**Ayar Alanları**

| Varlık                    | Tip             | Açıklama                                                                                                                                                                                                                                                                                        | Varsayılan Değer  |
|:------------------------- |:--------------- |:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |:----------------- |
| Çatallama öncesi          | `bool`          | [` SO_REUSEPORT`](https://lwn.net/Articles/542629/) soket seçeneğinin kullanılmasını sağlar. Bu, aynı bağlantı noktasını dinleyen birden fazla Go işlemi oluşturacaktır. [ soket parçalama ](https://www.nginx.com/blog/socket-sharding-nginx-release-1-9-1/) hakkında daha fazla bilgi edinin. | `false`           |
| Sunucu Başlığı            | `string`        | ` Sunucu ` HTTP başlığı verilen değerle etkinleştirir.                                                                                                                                                                                                                                          | `""`              |
| Sıkı Yönlendirme          | `bool`          | Etkinleştirildiğinde, yönlendirici ` / foo `'a ve ` / foo / `'a farklı gibi davranır. Aksi takdirde, yönlendirici ` / foo ` ve ` / foo / ` öğelerine aynı şekilde davranır.                                                                                                                     | `false`           |
| CaseSensitive             | `bool`          | Etkinleştirildiğinde, `/Foo` ve `/foo` için ayrı yönlendirmeler kullanılabilir. Aksi taktirde, `/Foo` ve `/foo`'nin ikisi de aynı yönlendirmeyi ifade eder.                                                                                                                                     | `false`           |
| Immutable                 | `bool`          | When enabled, all values returned by context methods are immutable. By default they are valid until you return from the handler, see issue [\#185](https://github.com/gofiber/fiber/issues/185).                                                                                              | `false`           |
| BodyLimit                 | `int`           | Sets the maximum allowed size for a request body, if the size exceeds the configured limit, it sends `413 - Request Entity Too Large` response.                                                                                                                                                 | `4 * 1024 * 1024` |
| Concurrency               | `int`           | Maximum number of concurrent connections.                                                                                                                                                                                                                                                       | `256 * 1024`      |
| DisableKeepalive          | `bool`          | Disable keep-alive connections, the server will close incoming connections after sending the first response to client                                                                                                                                                                           | `false`           |
| DisableDefaultDate        | `bool`          | When set to true causes the default date header to be excluded from the response.                                                                                                                                                                                                               | `false`           |
| DisableDefaultContentType | `bool`          | When set to true, causes the default Content-Type header to be excluded from the Response.                                                                                                                                                                                                      | `false`           |
| DisableStartupMessage     | `bool`          | When set to true, it will not print out the fiber ASCII and "listening" on message                                                                                                                                                                                                              | `false`           |
| DisableHeaderNormalizing  | `bool`          | By default all header names are normalized: conteNT-tYPE -&gt; Content-Type                                                                                                                                                                                                               | `false`           |
| ETag                      | `bool`          | Enable or disable ETag header generation, since both weak and strong etags are generated using the same hashing method \(CRC-32\). Weak ETags are the default when enabled.                                                                                                                   | `false`           |
| Templates                 | `Templates`     | Templates is the interface that wraps the Render function. See our [**Template Middleware**](middleware.md#template) for supported engines.                                                                                                                                                     | `nil`             |
| ReadTimeout               | `time.Duration` | The amount of time allowed to read the full request including body. Default timeout is unlimited.                                                                                                                                                                                               | `nil`             |
| WriteTimeout              | `time.Duration` | The maximum duration before timing out writes of the response. Default timeout is unlimited.                                                                                                                                                                                                    | `nil`             |
| IdleTimeout               | `time.Duration` | The maximum amount of time to wait for the next request when keep-alive is enabled. If IdleTimeout is zero, the value of ReadTimeout is used.                                                                                                                                                   | `nil`             |

## Static

Use the **Static** method to serve static files such as **images**, **CSS** and **JavaScript**.

{% hint style="info" %}
By default, **Static** will serve `index.html` files in response to a request on a directory.
{% endhint %}

{% code title="Signature" %}
```go
app.Static(prefix, root string, config ...Static) // => with prefix
```
{% endcode %}

Use the following code to serve files in a directory named `./public`

{% code title="Example" %}
```go
app.Static("/", "./public")

// => http://localhost:3000/hello.html
// => http://localhost:3000/js/jquery.js
// => http://localhost:3000/css/style.css
```
{% endcode %}

To serve from multiple directories, you can use **Static** multiple times.

{% code title="Example" %}
```go
// Serve files from "./public" directory:
app.Static("/", "./public")

// Serve files from "./files" directory:
app.Static("/", "./files")
```
{% endcode %}

{% hint style="info" %}
Use a reverse proxy cache like [**NGINX**](https://www.nginx.com/resources/wiki/start/topics/examples/reverseproxycachingexample/) to improve performance of serving static assets.
{% endhint %}

You can use any virtual path prefix \(_where the path does not actually exist in the file system_\) for files that are served by the **Static** method, specify a prefix path for the static directory, as shown below:

{% code title="Example" %}
```go
app.Static("/static", "./public")

// => http://localhost:3000/static/hello.html
// => http://localhost:3000/static/js/jquery.js
// => http://localhost:3000/static/css/style.css
```
{% endcode %}

If you want to have a little bit more control regarding the settings for serving static files. You could use the `fiber.Static` struct to enable specific settings.

{% code title="fiber.Static{}" %}
```go
// Static represents settings for serving static files
type Static struct {
    // Transparently compresses responses if set to true
    // This works differently than the github.com/gofiber/compression middleware
    // The server tries minimizing CPU usage by caching compressed files.
    // It adds ".fiber.gz" suffix to the original file name.
    // Optional. Default value false
    Compress bool
    // Enables byte range requests if set to true.
    // Optional. Default value false
    ByteRange bool
    // Enable directory browsing.
    // Optional. Default value false.
    Browse bool
    // Index file for serving a directory.
    // Optional. Default value "index.html".
    Index string
}
```
{% endcode %}

{% code title="Example" %}
```go
app.Static("/", "./public", fiber.Static{
  Compress:   true,
  ByteRange:  true,
  Browse:     true,
  Index:      "john.html"
})
```
{% endcode %}

## HTTP Methods

Routes an HTTP request, where **METHOD** is the [HTTP method](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods) of the request.

{% code title="Signatures" %}
```go
// HTTP methods support :param, :optional? and *wildcards
// You are required to pass a path to each method
app.All(path string, handlers ...func(*Ctx)) []*Route
app.Get
app.Put
app.Post
app.Head
app.Patch
app.Trace
app.Delete
app.Connect
app.Options

// Use() will only match the beggining of each path
// i.e. "/john" will match "/john/doe", "/johnnnn"
// Use() does not support :param & :optional? in path
app.Use(handlers ...func(*Ctx)) *Route
app.Use(prefix string, handlers ...func(*Ctx)) *Route
```
{% endcode %}

{% code title="Example" %}
```go
app.Use("/api", func(c *fiber.Ctx) {
  c.Set("X-Custom-Header", random.String(32))
  c.Next()
})
app.Get("/api/list", func(c *fiber.Ctx) {
  c.Send("I'm a GET request!")
})
app.Post("/api/register", func(c *fiber.Ctx) {
  c.Send("I'm a POST request!")
})
```
{% endcode %}

## Group

You can group routes by creating a `*Group` struct.

**Signature**

```go
app.Group(prefix string, handlers ...func(*Ctx)) *Group
```

**Example**

```go
func main() {
  app := fiber.New()

  api := app.Group("/api", handler)  // /api

  v1 := api.Group("/v1", handler)   // /api/v1
  v1.Get("/list", handler)          // /api/v1/list
  v1.Get("/user", handler)          // /api/v1/user

  v2 := api.Group("/v2", handler) // /api/v2
  v2.Get("/list", handler)          // /api/v2/list
  v2.Get("/user", handler)          // /api/v2/user

  app.Listen(3000)
}
```

## Listen

Binds and listens for connections on the specified address. This can be a `int` for port or `string` for address.

{% code title="Signature" %}
```go
app.Listen(address interface{}, tls ...*tls.Config) error
```
{% endcode %}

{% code title="Examples" %}
```go
app.Listen(8080)
app.Listen("8080")
app.Listen(":8080")
app.Listen("127.0.0.1:8080")
```
{% endcode %}

To enable **TLS/HTTPS** you can append a [**TLS config**](https://golang.org/pkg/crypto/tls/#Config).

{% code title="Example" %}
```go
cer, err := tls.LoadX509KeyPair("server.crt", "server.key")
if err != nil {
    log.Fatal(err)
}
config := &tls.Config{Certificates: []tls.Certificate{cer}}

app.Listen(443, config)
```
{% endcode %}

## Serve

You can pass your own [`net.Listener`](https://golang.org/pkg/net/#Listener) using the `Serve` method.

{% code title="Signature" %}
```go
app.Serve(ln net.Listener, tls ...*tls.Config) error
```
{% endcode %}

{% hint style="warning" %}
**Serve** does not support the **\*\*\[**Prefork\*\* \]\(application.md\#settings\)feature.
{% endhint %}

{% code title="Example" %}
```go
if ln, err = net.Listen("tcp4", ":8080"); err != nil {
    log.Fatal(err)
}

app.Serve(ln)
```
{% endcode %}

## Test

Testing your application is done with the **Test** method. Use this method for creating `_test.go` files or when you need to debug your routing logic. The default timeout is `200ms` if you want to disable a timeout completely, pass `-1` as a second argument.

{% code title="Signature" %}
```go
app.Test(req *http.Request, msTimeout ...int) (*http.Response, error)
```
{% endcode %}

{% code title="Example" %}
```go
// Create route with GET method for test:
app.Get("/", func(c *Ctx) {
  fmt.Println(c.BaseURL())              // => http://google.com
  fmt.Println(c.Get("X-Custom-Header")) // => hi

  c.Send("hello, World!")
})

// http.Request
req, _ := http.NewRequest("GET", "http://google.com", nil)
req.Header.Set("X-Custom-Header", "hi")

// http.Response
resp, _ := app.Test(req)

// Do something with results:
if resp.StatusCode == 200 {
  body, _ := ioutil.ReadAll(resp.Body)
  fmt.Println(string(body)) // => Hello, World!
}
```
{% endcode %}
