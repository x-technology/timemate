# TimeMate - How to make time & microservices work for you with Golang and GRPC

### Date And Time
Sat, December 18th, 2021 10:00 PM – 11:00 PM CET

- About us
- TimeMate
    - Goals
    - Docker
    - Kubernetes
    - Helm
    - Terraform
- Next Steps

# About us

**XTechnology** - *Educational Dev* open source project

## Goals

- learn new technologies
- share ideas and knowledge
- build online projects and community 🚀
- have fun! 👋

Copyright (c) 2021 x-technology

## [Andrew Red](https://twitter.com/AndrewRedUK), [mazahaca](https://github.com/mazahaca)

# TimeMate

## Goals

- introduce TimeMate project
- take a look into GRPC
- learn basic setup for golang repo
- design architecture of system: interfaces, plugins.
- pick a module system: native golang plugins or (g)RPC plugins
- quick look into brew package publication process

<img width="2632" alt="TimeMate-Flow" src="https://user-images.githubusercontent.com/1290067/146447368-3a9bc4a7-1399-436e-993b-a171f5b8b044.png">

## Idea

This is a demo project to automate daily rutines of consultancy agent (developer).

- track time over different platform, which you're comfortable with.
- synchronize tracked time entries with customer's software, e.g. Jira, YouTrack.
- create regular (monthly, weekly) time reports in PDF.
- create regular invoices (monthly, weekly) in PDF.
- send reports/invoices over various of channels, e.g. slack, email.
- reconcline invoices with bussiness account to mark that money is received.
- tax reporting

## GRPC

In gRPC, a client application can directly call a method on a server application on a different machine as if it were a local object, making it easier for you to create distributed applications and services. As in many RPC systems, gRPC is based around the idea of defining a service, specifying the methods that can be called remotely with their parameters and return types. On the server side, the server implements this interface and runs a gRPC server to handle client calls. On the client side, the client has a stub (referred to as just a client in some languages) that provides the same methods as the server.

![Grpc Schema](https://grpc.io/img/landing-2.svg)

### Protocol Buffers

By default, gRPC uses Protocol Buffers, Google’s mature open source mechanism for serializing structured data (although it can be used with other data formats such as JSON).

Let's have a look into our tracking api interface.

```protobuf
package tracker;

service TrackerApiService {
  rpc GetTimeEntries(GetTimeEntriesRequest) returns (GetTimeEntriesResponse) {}
}

message TimeEntry {
  string id = 1;
  string description = 2;
  string start = 3;
  string end = 4;
  repeated string tags = 5;
  string source = 6;
}

message GetTimeEntriesRequest {
  string start = 1;
  string end = 2;
}

message GetTimeEntriesResponse {
  repeated TimeEntry entries = 1;
}

```

## Plugins in Golang

### Compile-time plugins
Compile-time plugins consist of code packages that get compiled into the application's main binary. Once the binary is built, its functionality is fixed.

The best known example of a compile-time plugin system in Go is drivers for the `database/sql` package.
Usually Discovery and Registration of such plugins happens automatically, when you `import` a package.

### Run-time plugins
Run-time plugins consist of code that does not get compiled into the original binary of the main application; instead, it hooks up to this application at run-time. In compiled languages, a common tool to achieve this goal is *shared libraries*, and Go supports this approach as well.

Go comes with a plugin package built into the standard library. This package lets us write Go programs that get compiled into shared libraries instead of into executable binaries; further, it provides simple functions for loading shared libraries and getting symbols from them.

Discovery and Registration: accomplished via file system lookup.

However, this approach also has some serious downsides. The most important downside is that Go is very picky about keeping the main application and the shared libraries it loads compatible.

#### Alternatives?

RPC-based plugin systems!

[hashicorp/go-plugin](https://github.com/hashicorp/go-plugin) is one of the most popular.

It has several important upsides:

- Isolation: crash in a plugin does not bring the whole application down.
- Interoperability between languages: if RPC is the interface, do you care what language the plugin is written in?
- Distribution: if plugins interface via the network, we can easily distribute them to run on different machines for gains in performance, reliability, and so on.

Let's take a look into a code example

**Updating the Protocol**
If you update the protocol buffers file, you can regenerate the file using the following command from this directory. You do not need to run this if you're just trying the example.

```shell
protoc \
  --go_out=. \
  --go_opt=paths=source_relative \
  --go-grpc_out=. \
  --go-grpc_opt=paths=source_relative \
  ./proto/tracker.proto
```

# Next Steps

- Build first gRPC server/client
- Let's implement a couple of services on each side to see the whole picture
- Prepare TimeMate app for publication to [homebrew](https://brew.sh/)

## Resources

- [GRPC](https://grpc.io/docs/what-is-grpc/introduction/)
- [FigmaJam](https://www.figma.com/figjam/)
- [Plugins in Go](https://eli.thegreenplace.net/2021/plugins-in-go/)
- [Protobuf](https://developers.google.com/protocol-buffers/docs/reference/go-generated#package)

## Q/A

Any questions?
