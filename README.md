<p align="center">
    <img alt="Hippo Logo" src="https://raw.githubusercontent.com/Clivern/Hippo/master/assets/img/logo.png" height="100" />
    <h3 align="center">Hippo</h3>
    <p align="center">A Microservices Toolkit.</p>
    <p align="center">
        <a href="https://godoc.org/github.com/clivern/hippo"><img src="https://godoc.org/github.com/clivern/hippo?status.svg"></a>
        <a href="https://travis-ci.org/Clivern/Hippo"><img src="https://travis-ci.org/Clivern/Hippo.svg?branch=master"></a>
        <a href="https://github.com/Clivern/Hippo/releases"><img src="https://img.shields.io/badge/Version-1.0.0-red.svg"></a>
        <a href="https://goreportcard.com/report/github.com/Clivern/Hippo"><img src="https://goreportcard.com/badge/github.com/Clivern/Hippo?v=1.0.0"></a>
        <a href="https://github.com/Clivern/Hippo/blob/master/LICENSE"><img src="https://img.shields.io/badge/LICENSE-MIT-orange.svg"></a>
    </p>
</p>

Hippo is a collection of well crafted go packages that help you build robust, reliable, maintainable microservices. It is not a full-fledged framework with lot of magic, predefined architecture, specific patterns and bullshit opinions so you will be the one behind the wheel.

It provides libraries to implement components for service discovery, async jobs, authentication, authorization, logging, caching, metrics, tracing, rate-limiting...etc which are essential requirements for running microservices in production.

## Documentation

### Installation:

```golang
go get -u github.com/clivern/hippo
```
```golang
import (
    "github.com/clivern/hippo"
)
```

### Components:

**HTTP Requests Component**

```golang

httpClient := hippo.NewHTTPClient()

// Get Request
response, error := httpClient.Get(
    "https://httpbin.org/get",
    map[string]string{"url_arg_key": "url_arg_value"},
    map[string]string{"header_key": "header_value"},
)

// Delete Request
response, error := httpClient.Delete(
    "https://httpbin.org/delete",
    map[string]string{"url_arg_key": "url_arg_value"},
    map[string]string{"header_key": "header_value"},
)

// Post Request
response, error := httpClient.Post(
    "https://httpbin.org/post",
    `{"RequestBodyKey":"RequestBodyValue"}`,
    map[string]string{"url_arg_key": "url_arg_value"},
    map[string]string{"header_key": "header_value"},
)

// Put Request
response, error := httpClient.Put(
    "https://httpbin.org/put",
    `{"RequestBodyKey":"RequestBodyValue"}`,
    map[string]string{"url_arg_key": "url_arg_value"},
    map[string]string{"header_key": "header_value"},
)

// ....

statusCode := httpClient.GetStatusCode(response)
responseBody, error := httpClient.ToString(response)
```

**Time Series/Graphite Component**

```golang
import "time"


metric := hippo.NewMetric("hippo1.up", "23", time.Now().Unix()) // Type is hippo.Metric

metrics := NewMetrics("hippo2.up", "35", time.Now().Unix()) // type is []hippo.Metric
metrics = append(metrics, NewMetric("hippo2.down", "40", time.Now().Unix()))
metrics = append(metrics, NewMetric("hippo2.error", "70", time.Now().Unix()))

// NewGraphite(protocol string, host string, port int, prefix string)
// protocol can be tcp, udp or nop
// prefix is a metric prefix
graphite := hippo.NewGraphite("tcp", "127.0.0.1", 2003, "")
error := graphite.Connect()

if error == nil{
    // send one by one
    graphite.SendMetric(metric)

    // bulk send
    graphite.SendMetrics(metrics)
}
````

**System Stats Component**

```golang
// func NewSystemStats(enableCPU, enableMem, enableGC bool) *SystemStats {
stats := hippo.NewSystemStats(true, true, true)
stats.GetStats() // type map[string]uint64
// map[cpu.cgo_calls:0 cpu.goroutines:1 mem.alloc:0....]
```

**Correlation ID Component**

```golang
correlation := hippo.NewCorrelation()
correlation.UUIDv4()
```

**Workers Pool Component**

```golang
import "fmt"

tasks := []*hippo.Task{
    hippo.NewTask(func() (string, error) {
        fmt.Println("Task #1")
        return "Result 1", nil
    }),
    hippo.NewTask(func() (string, error) {
        fmt.Println("Task #2")
        return "Result 2", nil
    }),
    hippo.NewTask(func() (string, error) {
        fmt.Println("Task #3")
        return "Result 3", nil
    }),
}

// hippo.NewWorkersPool(tasks []*Task, concurrency int) *WorkersPool
p := hippo.NewWorkersPool(tasks, 2)
p.Run()

var numErrors int
for _, task := range p.Tasks {
    if task.Err != nil {
        fmt.Println(task.Err)
        numErrors++
    } else {
        fmt.Println(task.Result)
    }
    if numErrors >= 10 {
        fmt.Println("Too many errors.")
        break
    }
}
````

**Health Checker Component**

```golang
healthChecker := hippo.NewHealthChecker()
healthChecker.AddCheck("ping_check", func() (bool, error){
    return true, nil
})
healthChecker.AddCheck("db_check", func() (bool, error){
    return false, fmt.Errorf("Database Down")
})
healthChecker.RunChecks()

fmt.Println(healthChecker.ChecksStatus())
// Output -> DOWN
fmt.Println(healthChecker.ChecksReport())
// Output -> [{"id":"ping_check","status":"UP","error":"","result":true},{"id":"db_check","status":"DOWN","error":"Database Down","result":false}] <nil>
```

## Versioning

For transparency into our release cycle and in striving to maintain backward compatibility, Hippo is maintained under the [Semantic Versioning guidelines](https://semver.org/) and release process is predictable and business-friendly.

See the [Releases section of our GitHub project](https://github.com/clivern/hippo/releases) for changelogs for each release version of Hippo. It contains summaries of the most noteworthy changes made in each release.


## Bug tracker

If you have any suggestions, bug reports, or annoyances please report them to our issue tracker at https://github.com/clivern/hippo/issues


## Security Issues

If you discover a security vulnerability within Hippo, please send an email to [hello@clivern.com](mailto:hello@clivern.com)


## Contributing

We are an open source, community-driven project so please feel free to join us. see the [contributing guidelines](CONTRIBUTING.md) for more details.


## License

© 2019, Clivern. Released under [MIT License](https://opensource.org/licenses/mit-license.php).

**Hippo** is authored and maintained by [@Clivern](http://github.com/clivern).
