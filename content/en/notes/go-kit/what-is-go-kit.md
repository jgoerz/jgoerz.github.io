---
title: "What is go-kit?"
linkTitle: "What is go-kit?"
weight: 10
---

## What is go-kit?

It's a library for building go applications. So what's the big deal? 

Go-kit distinguishes itself from frameworks by focusing on a common set of
interfaces used to build go applications.  Rather than making most/all of the
decisions about concrete implementations, like a framework does, go-kit's goal
is to encourage an architecture that is maintainable as well as make it simple
to integrate with your current application (so called brownfield projects).


## How does it do it?

Go-kit targets the [hexagonal
architecture](https://alistair.cockburn.us/hexagonal-architecture), sometimes
referred to as _ports and adapters_ or _onion_ architecture.   Another, very
similar architecture you might be familiar with is the [clean
architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html). 

It does this by dividing the [architecture up in
layers](https://gokit.io/faq/#design-mdash-how-is-a-go-kit-microservice-modeled):
transport, endpoint, and service.

### Transport layer and abstraction

At its highest level of abstraction, go-kit is an implementation of the [template
method pattern](https://en.wikipedia.org/wiki/Template_method_pattern).  This
abstraction allows go-kit to support multiple types of transports that all
share very similar, if not exact, interfaces.

{{% pageinfo %}}
TL;DR

If you read the code, you can see the template method for 
[HTTP here](https://github.com/go-kit/kit/blob/v0.12.0/transport/http/server.go#L95),
[gRPC](https://github.com/go-kit/kit/blob/v0.12.0/transport/grpc/server.go#L90), 
[AMQP](https://github.com/go-kit/kit/blob/v0.12.0/transport/amqp/subscriber.go#L98),
and [NATS](https://github.com/go-kit/kit/blob/v0.12.0/transport/nats/subscriber.go#L94).
You can see the template in each of these transports' method and some
additional details not covered in these notes (like error handling).
{{% /pageinfo %}}

Most definitions of the pattern describe it using inheritance with subclasses
creating the concrete variations in behavior. In go, we use _interfaces_ to
make behavior pluggable.  This is what go-kit provides (although it does
provide some implementations with the defined interfaces).

If you're not familiar with the [template method
pattern](https://en.wikipedia.org/wiki/Template_method_pattern), the simplest
way to describe it is to show an example.  Notice in the example below, that
this method can do anything.  Its behavior is completely defined by the
behavior of the passed in interfaces.  The only thing that is concrete is the
order of operations: decode, transform, encode.  That is the template.  It's
also possible to make it generic using generics or reflection (using `any`
types).

```go
type Encoder interface {
  Encode(input string) (output string)
}
func (Encoder) Encode(input string) string {return input}

type Decoder interface {
  Decode(input string) (output string)
}
func (Decoder) Decode(input string) string {return input}

type Transformer interface {
  Transform(input string) (output string)
}
func (Transformer) Transform(input string) string {return input}

func TemplateMethod(input string, dec Decoder, enc Encoder, trans Transformer) string {
  result := dec.Decode(input)
  result = tran.Transform(result)
  return enc.Encode(result)
}
```

So from an order of operations point of view, go-kit defines each of the
innermost actions in the below _call graph_ as an interface that is pluggable.

```
# Simple call graph, HTTP example
HTTP request start
  => HTTP Server entry point
    => middleware ServeHTTP() chain
      =>  Before hook
      =>  DecodeRequestFunc called: decode(ctx, requ any) (resp any, err error)
      =>  Endpoint          called:     ep(ctx, requ any) (resp any, err error)
      =>  After hook
      =>  EncodeResponseFunc called: encode(ctx, requ any) (resp any, err error)
      =>  Finalizers called
    => middleware ServeHTTP() chain returns (if no early return)
  => HTTP Server exit point
HTTP request complete
```

This order of operations (or _template_) is supported for multiple transports.
This abstraction makes go-kit a very powerful choice for an extensible,
evolvable, and maintainable application architecture.

Here's a more concrete example of the same call graph with links to the
interfaces in go-kit's documentation as well as the location in the template method.

1. User creates an account at website: https://example.com/register
1. The server entrypoint identifies the route and calls the first middleware's
   [ServeHTTP()](https://pkg.go.dev/github.com/go-kit/kit@v0.12.0/transport/http#Server.ServeHTTP)
1. The HTTP transport's server
	 [invokes](https://github.com/go-kit/kit/blob/v0.12.0/transport/http/server.go#L114)
	 the assigned decode function of 
   [type DecodeRequestFunc](https://pkg.go.dev/github.com/go-kit/kit@v0.12.0/transport/http#DecodeRequestFunc)
   to convert the payload from HTTP format to the endpoint format.
1. The endpoint is [invoked](https://github.com/go-kit/kit/blob/v0.12.0/transport/http/server.go#L121),
	 receiving the decoded payload as an _interface_ (interface{} or any) type to the [endpoint
	 method](https://pkg.go.dev/github.com/go-kit/kit@v0.12.0/endpoint#Endpoint).
	 It typically implements the service interface and so invokes the service
   method assigned to this route.
1. The service method receives the domain payload and implements all of the
   business logic.  See [What does the workflow look
   like](#what-does-the-workflow-look-like). 
1. The service method returns a domain payload and/or error to the endpoint
   method.
1. The endpoint method returns the payload as an _interface_ (interface{} or
   any) type to the transport.
1. The transport invokes the assigned method with 
	 [type EncodeResponseFunc](https://pkg.go.dev/github.com/go-kit/kit@v0.12.0/transport/http#EncodeResponseFunc)
	 to [encode the
   payload](https://github.com/go-kit/kit/blob/v0.12.0/transport/http/server.go#L132)
   from an endpoint interface type to an HTTP payload.

### Endpoint layer and abstraction

Endpoints are the integration layer between transports and your services.  To
do this, go-kit defines an [endpoint
interface](https://pkg.go.dev/github.com/go-kit/kit@v0.12.0/endpoint#Endpoint).

```go
type Endpoint func(ctx context.Context, request interface{}) (response interface{}, err error)
// After go 1.18
type Endpoint func(ctx context.Context, request any) (response any, err error)
```

The purpose of the endpoint is to be an adapter for data exchange between the
transport layer and service layer. The most common way to use go-kit endpoints
is to create a factory function that returns an `Endpoint` type. 

```go
  func makeServiceMethodEP(srv Service) endpoint.Endpoint {
    return func(ctx context.Context, request any) (any, error) {

      requ := request.(*ServiceRequest)
      result, err := srv.ServiceMethod(ctx, requ)
      if err != nil {
        // https://github.com/go-kit/kit/blob/v0.12.0/transport/http/server.go#L14
        // Do not handle errors here, handle them with errorHandler and errorEncoder
        return nil, err
      }
      return result, err
    }
  }
```

The endpoint can now be invoked as a function passing in a context and a
request object and returning a response object and error.  This is so common,
that it effectively becomes boilerplate code that ought to be generated.

Go-kit also defines a [Middleware
type](https://pkg.go.dev/github.com/go-kit/kit@v0.12.0/endpoint#Middleware) for
the endpoint layer.  This allows for behavior that can be layered or stacked
upon each other.  The most common use case for this is metric collection or
client side balancing/limiting type of operations.

## What does the workflow look like?

The workflow is best described in the go-kit FAQ [[Bourgon]]({{<relref "#references">}}).

1. You implement the core business logic by **defining an interface for your
   service** and **providing a concrete implementation**.
1. Then, you write service middlewares to provide additional functionality,
   like logging, analytics, instrumentation — anything that needs knowledge of
   your business domain.
1. Finally, you add in endpoints and transports.

What this means is you can focus completely on your service layer using an
[in-memory repository](https://martinfowler.com/eaaCatalog/repository.html)
without regard for the transport or persistence.  If this seems strange or
backwards to you it is because of the rise of frameworks.  The presentation
["Architecture the Lost Years"](https://youtu.be/WpkDN78P884?t=511) by Robert
Martin describes the phenomenon.


## References

<!-- Format for online resources: -->
<!-- Author Last Name, First Name. “Title of Work.” Title of Site, Sponsor or -->
<!-- Publisher (include only if different from website title or author), Date of -->
<!-- Publication or Update Date, URL. Accessed Date (only if no date of publication -->
<!-- or update date). -->

1. Bourgon, Peter et al. "Go kit Frequently asked questions." Go kit A toolkit for microservices. Feb 25, 2023, [URL](https://gokit.io/faq)
2. Tensor.  "Building Microservices with the Go Kit Toolkit."  Oct. 10, 2019, [URL](https://www.youtube.com/watch?v=sjd2ePF3CuQ)
3. Ryer, Matt, "Go Programming Blueprints, 2nd Edition." Packt, Jan. 2015, [URL](https://github.com/PacktPublishing/Go-Programming-Blueprints/tree/master/Chapter10)
