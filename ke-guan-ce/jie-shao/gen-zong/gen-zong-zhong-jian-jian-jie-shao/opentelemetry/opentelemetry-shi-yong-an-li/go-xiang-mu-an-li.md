# Go项目案例

```
# cd http-sample/
​
# ls
Dockerfile  go.mod  go.sum  main.go  manifests  otel.go  README.md
```

```
# cat > Dockerfile <<EOF
FROM --platform=$BUILDPLATFORM golang:1.21.4 as builder
ARG TARGETOS
ARG TARGETARCH
​
WORKDIR /app
ENV GOPROXY=https://goproxy.cn,direct
COPY go.mod ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 GOOS=$TARGETOS GOARCH=$TARGETARCH go build -a -installsuffix cgo -o main .
​
FROM --platform=$BUILDPLATFORM golang:1.21.4
WORKDIR /
COPY --from=builder /app/main .
EXPOSE 8080
CMD ["./main"]
EOF
```

```
# cat > go.mod <<EOF
module http-sample
​
go 1.21.4
​
require (
        go.opentelemetry.io/contrib/instrumentation/net/http/otelhttp v0.46.1
        go.opentelemetry.io/otel v1.21.0
        go.opentelemetry.io/otel/exporters/otlp/otlpmetric/otlpmetrichttp v0.44.0
        go.opentelemetry.io/otel/exporters/otlp/otlptrace v1.21.0
        go.opentelemetry.io/otel/exporters/otlp/otlptrace/otlptracehttp v1.21.0
        go.opentelemetry.io/otel/sdk v1.21.0
        go.opentelemetry.io/otel/sdk/metric v1.21.0
)
​
require (
        github.com/cenkalti/backoff/v4 v4.2.1 // indirect
        github.com/felixge/httpsnoop v1.0.4 // indirect
        github.com/go-logr/logr v1.3.0 // indirect
        github.com/go-logr/stdr v1.2.2 // indirect
        github.com/golang/protobuf v1.5.3 // indirect
        github.com/grpc-ecosystem/grpc-gateway/v2 v2.16.0 // indirect
        go.opentelemetry.io/contrib/propagators/b3 v1.21.1 // indirect
        go.opentelemetry.io/contrib/propagators/jaeger v1.21.1 // indirect
        go.opentelemetry.io/otel/metric v1.21.0 // indirect
        go.opentelemetry.io/otel/trace v1.21.0 // indirect
        go.opentelemetry.io/proto/otlp v1.0.0 // indirect
        golang.org/x/net v0.17.0 // indirect
        golang.org/x/sys v0.14.0 // indirect
        golang.org/x/text v0.13.0 // indirect
        google.golang.org/genproto/googleapis/api v0.0.0-20230822172742-b8732ec3820d // indirect
        google.golang.org/genproto/googleapis/rpc v0.0.0-20230822172742-b8732ec3820d // indirect
        google.golang.org/grpc v1.59.0 // indirect
        google.golang.org/protobuf v1.31.0 // indirect
)
EOF
```

```
# cat > go.sum <<EOF
github.com/cenkalti/backoff/v4 v4.2.1 h1:y4OZtCnogmCPw98Zjyt5a6+QwPLGkiQsYW5oUqylYbM=
github.com/cenkalti/backoff/v4 v4.2.1/go.mod h1:Y3VNntkOUPxTVeUxJ/G5vcM//AlwfmyYozVcomhLiZE=
github.com/davecgh/go-spew v1.1.1 h1:vj9j/u1bqnvCEfJOwUhtlOARqs3+rkHYY13jYWTU97c=
github.com/davecgh/go-spew v1.1.1/go.mod h1:J7Y8YcW2NihsgmVo/mv3lAwl/skON4iLHjSsI+c5H38=
github.com/felixge/httpsnoop v1.0.4 h1:NFTV2Zj1bL4mc9sqWACXbQFVBBg2W3GPvqp8/ESS2Wg=
github.com/felixge/httpsnoop v1.0.4/go.mod h1:m8KPJKqk1gH5J9DgRY2ASl2lWCfGKXixSwevea8zH2U=
github.com/go-logr/logr v1.2.2/go.mod h1:jdQByPbusPIv2/zmleS9BjJVeZ6kBagPoEUsqbVz/1A=
github.com/go-logr/logr v1.3.0 h1:2y3SDp0ZXuc6/cjLSZ+Q3ir+QB9T/iG5yYRXqsagWSY=
github.com/go-logr/logr v1.3.0/go.mod h1:9T104GzyrTigFIr8wt5mBrctHMim0Nb2HLGrmQ40KvY=
github.com/go-logr/stdr v1.2.2 h1:hSWxHoqTgW2S2qGc0LTAI563KZ5YKYRhT3MFKZMbjag=
github.com/go-logr/stdr v1.2.2/go.mod h1:mMo/vtBO5dYbehREoey6XUKy/eSumjCCveDpRre4VKE=
github.com/golang/glog v1.1.2 h1:DVjP2PbBOzHyzA+dn3WhHIq4NdVu3Q+pvivFICf/7fo=
github.com/golang/glog v1.1.2/go.mod h1:zR+okUeTbrL6EL3xHUDxZuEtGv04p5shwip1+mL/rLQ=
github.com/golang/protobuf v1.5.0/go.mod h1:FsONVRAS9T7sI+LIUmWTfcYkHO4aIWwzhcaSAoJOfIk=
github.com/golang/protobuf v1.5.3 h1:KhyjKVUg7Usr/dYsdSqoFveMYd5ko72D+zANwlG1mmg=
github.com/golang/protobuf v1.5.3/go.mod h1:XVQd3VNwM+JqD3oG2Ue2ip4fOMUkwXdXDdiuN0vRsmY=
github.com/google/go-cmp v0.5.5/go.mod h1:v8dTdLbMG2kIc/vJvl+f65V22dbkXbowE6jgT/gNBxE=
github.com/google/go-cmp v0.6.0 h1:ofyhxvXcZhMsU5ulbFiLKl/XBFqE1GSq7atu8tAmTRI=
github.com/google/go-cmp v0.6.0/go.mod h1:17dUlkBOakJ0+DkrSSNjCkIjxS6bF9zb3elmeNGIjoY=
github.com/grpc-ecosystem/grpc-gateway/v2 v2.16.0 h1:YBftPWNWd4WwGqtY2yeZL2ef8rHAxPBD8KFhJpmcqms=
github.com/grpc-ecosystem/grpc-gateway/v2 v2.16.0/go.mod h1:YN5jB8ie0yfIUg6VvR9Kz84aCaG7AsGZnLjhHbUqwPg=
github.com/pmezard/go-difflib v1.0.0 h1:4DBwDE0NGyQoBHbLQYPwSUPoCMWR5BEzIk/f1lZbAQM=
github.com/pmezard/go-difflib v1.0.0/go.mod h1:iKH77koFhYxTK1pcRnkKkqfTogsbg7gZNVY4sRDYZ/4=
github.com/stretchr/testify v1.8.4 h1:CcVxjf3Q8PM0mHUKJCdn+eZZtm5yQwehR5yeSVQQcUk=
github.com/stretchr/testify v1.8.4/go.mod h1:sz/lmYIOXD/1dqDmKjjqLyZ2RngseejIcXlSw2iwfAo=
go.opentelemetry.io/contrib/instrumentation/net/http/otelhttp v0.46.1 h1:aFJWCqJMNjENlcleuuOkGAPH82y0yULBScfXcIEdS24=
go.opentelemetry.io/contrib/instrumentation/net/http/otelhttp v0.46.1/go.mod h1:sEGXWArGqc3tVa+ekntsN65DmVbVeW+7lTKTjZF3/Fo=
go.opentelemetry.io/contrib/propagators/b3 v1.21.1 h1:WPYiUgmw3+b7b3sQ1bFBFAf0q+Di9dvNc3AtYfnT4RQ=
go.opentelemetry.io/contrib/propagators/b3 v1.21.1/go.mod h1:EmzokPoSqsYMBVK4nRnhsfm5mbn8J1eDuz/U1UaQaWg=
go.opentelemetry.io/contrib/propagators/jaeger v1.21.1 h1:f4beMGDKiVzg9IcX7/VuWVy+oGdjx3dNJ72YehmtY5k=
go.opentelemetry.io/contrib/propagators/jaeger v1.21.1/go.mod h1:U9jhkEl8d1LL+QXY7q3kneJWJugiN3kZJV2OWz3hkBY=
go.opentelemetry.io/otel v1.21.0 h1:hzLeKBZEL7Okw2mGzZ0cc4k/A7Fta0uoPgaJCr8fsFc=
go.opentelemetry.io/otel v1.21.0/go.mod h1:QZzNPQPm1zLX4gZK4cMi+71eaorMSGT3A4znnUvNNEo=
go.opentelemetry.io/otel/exporters/otlp/otlpmetric/otlpmetrichttp v0.44.0 h1:bflGWrfYyuulcdxf14V6n9+CoQcu5SAAdHmDPAJnlps=
go.opentelemetry.io/otel/exporters/otlp/otlpmetric/otlpmetrichttp v0.44.0/go.mod h1:qcTO4xHAxZLaLxPd60TdE88rxtItPHgHWqOhOGRr0as=
go.opentelemetry.io/otel/exporters/otlp/otlptrace v1.21.0 h1:cl5P5/GIfFh4t6xyruOgJP5QiA1pw4fYYdv6nc6CBWw=
go.opentelemetry.io/otel/exporters/otlp/otlptrace v1.21.0/go.mod h1:zgBdWWAu7oEEMC06MMKc5NLbA/1YDXV1sMpSqEeLQLg=
go.opentelemetry.io/otel/exporters/otlp/otlptrace/otlptracehttp v1.21.0 h1:digkEZCJWobwBqMwC0cwCq8/wkkRy/OowZg5OArWZrM=
go.opentelemetry.io/otel/exporters/otlp/otlptrace/otlptracehttp v1.21.0/go.mod h1:/OpE/y70qVkndM0TrxT4KBoN3RsFZP0QaofcfYrj76I=
go.opentelemetry.io/otel/metric v1.21.0 h1:tlYWfeo+Bocx5kLEloTjbcDwBuELRrIFxwdQ36PlJu4=
go.opentelemetry.io/otel/metric v1.21.0/go.mod h1:o1p3CA8nNHW8j5yuQLdc1eeqEaPfzug24uvsyIEJRWM=
go.opentelemetry.io/otel/sdk v1.21.0 h1:FTt8qirL1EysG6sTQRZ5TokkU8d0ugCj8htOgThZXQ8=
go.opentelemetry.io/otel/sdk v1.21.0/go.mod h1:Nna6Yv7PWTdgJHVRD9hIYywQBRx7pbox6nwBnZIxl/E=
go.opentelemetry.io/otel/sdk/metric v1.21.0 h1:smhI5oD714d6jHE6Tie36fPx4WDFIg+Y6RfAY4ICcR0=
go.opentelemetry.io/otel/sdk/metric v1.21.0/go.mod h1:FJ8RAsoPGv/wYMgBdUJXOm+6pzFY3YdljnXtv1SBE8Q=
go.opentelemetry.io/otel/trace v1.21.0 h1:WD9i5gzvoUPuXIXH24ZNBudiarZDKuekPqi/E8fpfLc=
go.opentelemetry.io/otel/trace v1.21.0/go.mod h1:LGbsEB0f9LGjN+OZaQQ26sohbOmiMR+BaslueVtS/qQ=
go.opentelemetry.io/proto/otlp v1.0.0 h1:T0TX0tmXU8a3CbNXzEKGeU5mIVOdf0oykP+u2lIVU/I=
go.opentelemetry.io/proto/otlp v1.0.0/go.mod h1:Sy6pihPLfYHkr3NkUbEhGHFhINUSI/v80hjKIs5JXpM=
golang.org/x/net v0.17.0 h1:pVaXccu2ozPjCXewfr1S7xza/zcXTity9cCdXQYSjIM=
golang.org/x/net v0.17.0/go.mod h1:NxSsAGuq816PNPmqtQdLE42eU2Fs7NoRIZrHJAlaCOE=
golang.org/x/sys v0.14.0 h1:Vz7Qs629MkJkGyHxUlRHizWJRG2j8fbQKjELVSNhy7Q=
golang.org/x/sys v0.14.0/go.mod h1:/VUhepiaJMQUp4+oa/7Zr1D23ma6VTLIYjOOTFZPUcA=
golang.org/x/text v0.13.0 h1:ablQoSUd0tRdKxZewP80B+BaqeKJuVhuRxj/dkrun3k=
golang.org/x/text v0.13.0/go.mod h1:TvPlkZtksWOMsz7fbANvkp4WM8x/WCo/om8BMLbz+aE=
golang.org/x/xerrors v0.0.0-20191204190536-9bdfabe68543/go.mod h1:I/5z698sn9Ka8TeJc9MKroUUfqBBauWjQqLJ2OPfmY0=
google.golang.org/genproto v0.0.0-20230822172742-b8732ec3820d h1:VBu5YqKPv6XiJ199exd8Br+Aetz+o08F+PLMnwJQHAY=
google.golang.org/genproto v0.0.0-20230822172742-b8732ec3820d/go.mod h1:yZTlhN0tQnXo3h00fuXNCxJdLdIdnVFVBaRJ5LWBbw4=
google.golang.org/genproto/googleapis/api v0.0.0-20230822172742-b8732ec3820d h1:DoPTO70H+bcDXcd39vOqb2viZxgqeBeSGtZ55yZU4/Q=
google.golang.org/genproto/googleapis/api v0.0.0-20230822172742-b8732ec3820d/go.mod h1:KjSP20unUpOx5kyQUFa7k4OJg0qeJ7DEZflGDu2p6Bk=
google.golang.org/genproto/googleapis/rpc v0.0.0-20230822172742-b8732ec3820d h1:uvYuEyMHKNt+lT4K3bN6fGswmK8qSvcreM3BwjDh+y4=
google.golang.org/genproto/googleapis/rpc v0.0.0-20230822172742-b8732ec3820d/go.mod h1:+Bk1OCOj40wS2hwAMA+aCW9ypzm63QTBBHp6lQ3p+9M=
google.golang.org/grpc v1.59.0 h1:Z5Iec2pjwb+LEOqzpB2MR12/eKFhDPhuqW91O+4bwUk=
google.golang.org/grpc v1.59.0/go.mod h1:aUPDwccQo6OTjy7Hct4AfBPD1GptF4fyUjIkQ9YtF98=
google.golang.org/protobuf v1.26.0-rc.1/go.mod h1:jlhhOSvTdKEhbULTjvd4ARK9grFBp09yW+WbY/TyQbw=
google.golang.org/protobuf v1.26.0/go.mod h1:9q0QmTI4eRPtz6boOQmLYwt+qCgq0jsYwAQnmE0givc=
google.golang.org/protobuf v1.31.0 h1:g0LDEJHgrBl9N9r17Ru3sqWhkIx2NB67okBHPwC7hs8=
google.golang.org/protobuf v1.31.0/go.mod h1:HV8QOd/L58Z+nl8r43ehVNZIU/HEI6OcFqwMG9pJV4I=
gopkg.in/yaml.v3 v3.0.1 h1:fxVm/GzAzEWqLHuvctI91KS9hhNmmWOoWu0XTYJS7CA=
gopkg.in/yaml.v3 v3.0.1/go.mod h1:K4uyk7z7BCEPqu6E+C64Yfv1cQ7kz7rIZviUmN+EgEM=
EOF
```

```
# cat > main.go <<EOF
package main
​
import (
        "context"
        "errors"
        "fmt"
        "go.opentelemetry.io/contrib/instrumentation/net/http/otelhttp"
        "io/ioutil"
        "net"
        "net/http"
        "os"
        "os/signal"
        "time"
)
​
const (
        IdentityHeader = "Identity"
        App            = "app"
        Version        = "version"
        Upstream       = "upstream"
        Port           = "port"
)
​
var upstream = os.Getenv(Upstream)
var appName = os.Getenv(App)
var version = os.Getenv(Version)
var port = os.Getenv(Port)
​
func main() {
        if port == "" {
                port = "8080"
        }
​
        // Handle SIGINT (CTRL+C) gracefully.
        ctx, stop := signal.NotifyContext(context.Background(), os.Interrupt)
        defer stop()
​
        // Set up OpenTelemetry.
        otelShutdown, err := setupOTelSDK(ctx, appName, version)
​
        // Handle shutdown properly so nothing leaks.
        defer func() {
                err = errors.Join(err, otelShutdown(context.Background()))
        }()
​
        // Start HTTP server.
        srv := &http.Server{
                Addr:         ":" + port,
                Handler:      newHTTPHandler(),
                WriteTimeout: 10 * time.Second,
                BaseContext:  func(_ net.Listener) context.Context { return ctx },
        }
​
        srvErr := make(chan error, 1)
        go func() {
                srvErr <- srv.ListenAndServe()
        }()
​
        // Wait for interruption.
        select {
        case err = <-srvErr:
                // Error when starting HTTP server.
                return
        case <-ctx.Done():
                // Wait for first CTRL+C.
                // Stop receiving signal notifications as soon as possible.
                stop()
        }
​
        // When Shutdown is called, ListenAndServe immediately returns ErrServerClosed.
        err = srv.Shutdown(context.Background())
        return
}
​
func newHTTPHandler() http.Handler {
        mux := http.NewServeMux()
​
        // handleFunc is a replacement for mux.HandleFunc
        // which enriches the handler's HTTP instrumentation with the pattern as the http.route.
        handleFunc := func(pattern string, handlerFunc func(http.ResponseWriter, *http.Request)) {
                // Configure the "http.route" for the HTTP instrumentation.
                handler := otelhttp.WithRouteTag(pattern, http.HandlerFunc(handlerFunc))
                mux.Handle(pattern, handler)
        }
​
        // Register handlers.
        handleFunc("/", handle)
​
        // Add HTTP instrumentation for the whole server.
        handler := otelhttp.NewHandler(mux, "/")
        return handler
}
​
func handle(w http.ResponseWriter, r *http.Request) {
        ip, hostname := getIPAndHostname()
        response := fmt.Sprintf("%s(version: %s, ip: %s, hostname: %s)", appName, version, ip, hostname)
​
        if upstream != "" {
                client := &http.Client{Transport: otelhttp.NewTransport(http.DefaultTransport)}
                req, _ := http.NewRequestWithContext(r.Context(), "GET", upstream, nil)
​
                for name, value := range getTracingHeaders(r) {
                        req.Header.Set(name, value)
                }
​
                upstreamResponse, err := client.Do(req)
                if err != nil {
                        fmt.Fprintf(w, "Error contacting upstream service: %v", err)
                        return
                }
                defer upstreamResponse.Body.Close()
                body, err := ioutil.ReadAll(upstreamResponse.Body)
                if err != nil {
                        fmt.Fprintf(w, "Error reading upstream response: %v", err)
                        return
                }
                response += fmt.Sprintf(" -> %s", string(body))
        }
​
        setHeaders(w, r)
        fmt.Fprintf(w, response)
}
​
func getIPAndHostname() (string, string) {
        host, _ := os.Hostname()
        addrs, _ := net.LookupIP(host)
        var ip string
        for _, addr := range addrs {
                if ipv4 := addr.To4(); ipv4 != nil {
                        ip = ipv4.String()
                        break
                }
        }
        return ip, host
}
​
func setHeaders(w http.ResponseWriter, r *http.Request) {
        w.Header().Set(IdentityHeader, os.Getenv(App))
​
        if r == nil {
                return
        }
​
        for _, header := range getTracingHeaderKeys() {
                if v := r.Header.Get(header); v != "" {
                        w.Header().Set(header, v)
                }
        }
}
​
func getTracingHeaderKeys() []string {
        return []string{"X-Ot-Span-Context", "Traceparent", "X-Request-Id", "uber-trace-id", "x-b3-traceid", "x-b3-spanid", "x-b3-parentspanid"}
}
​
func getTracingHeaders(r *http.Request) map[string]string {
        var headers = map[string]string{}
        for _, key := range getTracingHeaderKeys() {
                if v := r.Header.Get(key); v != "" {
                        headers[key] = v
                }
        }
​
        return headers
}
EOF
```

```
# cat > otel.go <<EOF
package main
​
import (
        "context"
        "errors"
        "fmt"
        "go.opentelemetry.io/contrib/propagators/b3"
        "go.opentelemetry.io/contrib/propagators/jaeger"
        "go.opentelemetry.io/otel/exporters/otlp/otlpmetric/otlpmetrichttp"
        "go.opentelemetry.io/otel/exporters/otlp/otlptrace"
        "go.opentelemetry.io/otel/exporters/otlp/otlptrace/otlptracehttp"
        "os"
        "strings"
        "time"
​
        "go.opentelemetry.io/otel"
        "go.opentelemetry.io/otel/propagation"
        "go.opentelemetry.io/otel/sdk/metric"
        "go.opentelemetry.io/otel/sdk/resource"
        "go.opentelemetry.io/otel/sdk/trace"
        sdktrace "go.opentelemetry.io/otel/sdk/trace"
        semconv "go.opentelemetry.io/otel/semconv/v1.21.0"
)
​
const OtelExporterOTLPEndpoints = "OTEL_EXPORTER_OTLP_ENDPOINT"
const OtelPropagators = "OTEL_PROPAGATORS"
​
// setupOTelSDK bootstraps the OpenTelemetry pipeline.
// If it does not return an error, make sure to call shutdown for proper cleanup.
func setupOTelSDK(ctx context.Context, serviceName, serviceVersion string) (shutdown func(context.Context) error, err error) {
        var shutdownFuncs []func(context.Context) error
​
        if os.Getenv(OtelExporterOTLPEndpoints) == "" {
                fmt.Println("OTEL_EXPORTER_OTLP_ENDPOINT not provided, skip!!!")
                return
        }
​
        // shutdown calls cleanup functions registered via shutdownFuncs.
        // The errors from the calls are joined.
        // Each registered cleanup will be invoked once.
        shutdown = func(ctx context.Context) error {
                var err error
                for _, fn := range shutdownFuncs {
                        err = errors.Join(err, fn(ctx))
                }
                shutdownFuncs = nil
                return err
        }
​
        // handleErr calls shutdown for cleanup and makes sure that all errors are returned.
        handleErr := func(inErr error) {
                err = errors.Join(inErr, shutdown(ctx))
        }
​
        // Set up resource.
        res, err := newResource(serviceName, serviceVersion)
        if err != nil {
                handleErr(err)
                return
        }
​
        // Set up propagator.
        prop := newPropagator()
        otel.SetTextMapPropagator(prop)
​
        // Set up trace provider.
        tracerProvider, err := newTraceProvider(res)
        if err != nil {
                handleErr(err)
                return
        }
        shutdownFuncs = append(shutdownFuncs, tracerProvider.Shutdown)
        otel.SetTracerProvider(tracerProvider)
​
        // Set up meter provider.
        meterProvider, err := newMeterProvider(res)
        if err != nil {
                handleErr(err)
                return
        }
        shutdownFuncs = append(shutdownFuncs, meterProvider.Shutdown)
        otel.SetMeterProvider(meterProvider)
​
        return
}
​
func newResource(serviceName, serviceVersion string) (*resource.Resource, error) {
        return resource.Merge(resource.Default(),
                resource.NewWithAttributes(semconv.SchemaURL,
                        semconv.ServiceName(serviceName),
                        semconv.ServiceVersion(serviceVersion),
                ))
}
​
func newPropagator() propagation.TextMapPropagator {
        propagatorString := os.Getenv(OtelPropagators)
        var propagators []propagation.TextMapPropagator
        if propagatorString != "" {
                for _, p := range strings.Split(propagatorString, ",") {
                        switch p {
                        case "tracecontext":
                                propagators = append(propagators, propagation.TraceContext{})
                        case "b3":
                                propagators = append(propagators, b3.New(b3.WithInjectEncoding(b3.B3SingleHeader)))
                        case "b3multi":
                                propagators = append(propagators, b3.New(b3.WithInjectEncoding(b3.B3MultipleHeader)))
                        case "baggage":
                                propagators = append(propagators, propagation.Baggage{})
                        case "jaeger":
                                propagators = append(propagators, jaeger.Jaeger{})
                        }
                }
        }
        if len(propagators) == 0 {
                propagators = append(propagators, b3.New(b3.WithInjectEncoding(b3.B3SingleHeader)))
        }
        return propagation.NewCompositeTextMapPropagator(propagators...)
}
​
func newTraceProvider(res *resource.Resource) (*trace.TracerProvider, error) {
        traceExporter, err := newTraceExporter()
        if err != nil {
                return nil, fmt.Errorf("failed to create trace provider: %w", err)
        }
​
        bsp := sdktrace.NewBatchSpanProcessor(traceExporter)
        tracerProvider := sdktrace.NewTracerProvider(
                sdktrace.WithSampler(sdktrace.AlwaysSample()),
                sdktrace.WithResource(res),
                sdktrace.WithSpanProcessor(bsp),
        )
        return tracerProvider, nil
}
​
func newTraceExporter() (*otlptrace.Exporter, error) {
        ctx := context.Background()
​
        ctx, cancel := context.WithTimeout(ctx, 5*time.Second)
        defer cancel()
        traceExporter, err := otlptracehttp.New(ctx, otlptracehttp.WithEndpoint(os.Getenv(OtelExporterOTLPEndpoints)), otlptracehttp.WithInsecure())
        if err != nil {
                return nil, fmt.Errorf("failed to create trace exporter: %w", err)
        }
        return traceExporter, nil
}
​
func newMeterProvider(res *resource.Resource) (*metric.MeterProvider, error) {
        metricExporter, err := newMetricExporter()
        if err != nil {
                return nil, fmt.Errorf("failed to create meter provider: %w", err)
        }
​
        meterProvider := metric.NewMeterProvider(
                metric.WithResource(res),
                metric.WithReader(metric.NewPeriodicReader(metricExporter,
                        // Default is 1m. Set to 5s for demonstrative purposes.
                        metric.WithInterval(5*time.Second))),
        )
        return meterProvider, nil
}
​
func newMetricExporter() (*otlpmetrichttp.Exporter, error) {
        ctx := context.Background()
​
        ctx, cancel := context.WithTimeout(ctx, 5*time.Second)
        defer cancel()
​
        metricExporter, err := otlpmetrichttp.New(ctx, otlpmetrichttp.WithEndpoint(os.Getenv(OtelExporterOTLPEndpoints)), otlpmetrichttp.WithInsecure())
        if err != nil {
                return nil, fmt.Errorf("failed to create metrics exporter: %w", err)
        }
​
        return metricExporter, nil
}
EOF
```

> 这段代码是一个使用 Go 语言编写的 OpenTelemetry SDK 设置和初始化函数。其主要功能是配置和启动一个完整的 OpenTelemetry 监控管道，包括资源定义、传播器设置、跟踪提供者和度量提供者的创建以及相应的 OTLP（OpenTelemetry Protocol）Exporter 的配置。

```
# ls manifests/
service-v1.yaml
​
# cat > manifests/service-v1.yaml <<EOF
apiVersion: v1
kind: ServiceAccount
metadata:
  name: service-a
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: service-a
  labels:
    app: service-a
    version: v1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: service-a
  template:
    metadata:
      labels:
        app: service-a
        version: v1
      annotations:
        instrumentation.opentelemetry.io/inject-sdk: "true"
        instrumentation.opentelemetry.io/container-names: "service-a"
    spec:
      serviceAccountName: service-a
      containers:
        - name: service-a
          image: www.kubemsb.com/library/http-sample:v1
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 8080
          env:
            - name: app
              value: "service-a"
            - name: version
              value: "v1"
            - name: upstream
              value: "http://service-b:8080/"
---
apiVersion: v1
kind: Service
metadata:
  name: service-a
  labels:
    app: service-a
spec:
  type: LoadBalancer
  selector:
    app: service-a
    version: v1
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 8080
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: service-b
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: service-b
  labels:
    app: service-b
    version: v1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: service-b
  template:
    metadata:
      labels:
        app: service-b
        version: v1
      annotations:
        instrumentation.opentelemetry.io/inject-sdk: "true"
        instrumentation.opentelemetry.io/container-names: "service-b"
    spec:
      serviceAccountName: service-b
      containers:
        - name: service-b
          image: www.kubemsb.com/library/http-sample:v1
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 8080
          env:
            - name: app
              value: "service-b"
            - name: version
              value: "v1"
            - name: upstream
              value: "http://service-c:8080/"
---
apiVersion: v1
kind: Service
metadata:
  name: service-b
  labels:
    app: service-b
spec:
  type: LoadBalancer
  selector:
    app: service-b
    version: v1
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 8080
​
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: service-c
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: service-c
  labels:
    app: service-c
    version: v1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: service-c
  template:
    metadata:
      labels:
        app: service-c
        version: v1
      annotations:
        instrumentation.opentelemetry.io/inject-sdk: "true"
        instrumentation.opentelemetry.io/container-names: "service-c"
    spec:
      serviceAccountName: service-c
      containers:
        - name: service-c
          image: www.kubemsb.com/library/http-sample:v1
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 8080
          env:
            - name: app
              value: "service-c"
            - name: version
              value: "v1"
---
apiVersion: v1
kind: Service
metadata:
  name: service-c
  labels:
    app: service-c
spec:
  type: LoadBalancer
  selector:
    app: service-c
    version: v1
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 8080
EOF
```

```
# docker build -t www.kubemsb.com/library/http-sample:v1 .
```

> 上传到本地镜像仓库更佳。

```
# docker save -o http-sample-v1.tar www.kubemsb.com/library/http-sample:v1
```

```
# for i in 161 162 163 164 ; do scp http-sample-v1.tar 192.168.10.$i:/root; done
```

```
# kubectl apply -f manifests/service-v1.yaml
```

```
# kubectl get svc
NAME                        TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)                                                         AGE
jaeger                      LoadBalancer   10.111.96.244   192.168.10.241   16686:32520/TCP,14268:31845/TCP,4318:30086/TCP,4317:32169/TCP   6h25m
java-sample                 LoadBalancer   10.98.13.169    192.168.10.242   8080:30614/TCP                                                  4h47m
kubernetes                  ClusterIP      10.96.0.1       <none>           443/TCP                                                         6d2h
otel-collector              ClusterIP      10.107.67.11    <none>           4317/TCP,4318/TCP                                               5h44m
otel-collector-headless     ClusterIP      None            <none>           4317/TCP,4318/TCP                                               5h44m
otel-collector-monitoring   ClusterIP      10.111.89.46    <none>           8888/TCP                                                        5h44m
service-a                   LoadBalancer   10.97.117.218   192.168.10.243   8080:31495/TCP                                                  25s
service-b                   LoadBalancer   10.108.41.212   192.168.10.244   8080:32050/TCP                                                  25s
service-c                   LoadBalancer   10.106.93.36    192.168.10.245   8080:30310/TCP                                                  25s
```

\
