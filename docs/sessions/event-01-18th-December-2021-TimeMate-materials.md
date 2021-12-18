# TimeMate - How to make time & microservices work for you with Golang and GRPC

### Date And Time
Sat, December 18th, 2021 10:00 PM – 11:00 PM CET

# About us

**XTechnology** - *Educational Dev* open source project

## Goals

- learn new technologies
- share ideas and knowledge
- build online projects and community 🚀
- have fun! 👋

Copyright (c) 2021 x-technology

<img width="25" alt="TimeMate-Flow" src="https://cdn4.iconfinder.com/data/icons/social-media-icons-the-circle-set/48/twitter_circle-512.png" />

[AndrewRedUK](https://twitter.com/AndrewRedUK)

# TimeMate

## Goals

- introduce TimeMate project
- take a look into GRPC
- quick look into brew package publication process
- design architecture of system: interfaces, plugins.
- pick a module system: native golang plugins or (g)RPC plugins

<img width="2632" alt="TimeMate-Flow" src="https://user-images.githubusercontent.com/1290067/146447368-3a9bc4a7-1399-436e-993b-a171f5b8b044.png">

## Problem?

I'm a software engineer and a tech consultant in various companies. My daily job is to design, discuss and build web projects and micro-service architectures.
However, there is one thing I don't really like - once a month I need to sit for the whole day and start preparing reports, invoices, bills, sending it over. Also, every day I need to report "timings" in a dedicated customer's software, e.g. Jira.
To help with that task I have multiple paid tools and still spend 1 whole day a month with it:
- Jira, Toggle, Invoicing software, Business account software, Slack, Email.

## Idea

This is a demo project to automate daily routines of consultancy agent (developer).

- track time over different platform, which you're comfortable with.
- synchronize tracked time entries with a customer's software, e.g. Jira, YouTrack.
- create regular (monthly, weekly) time reports in a PDF.
- create regular invoices (monthly, weekly) in a PDF.
- send reports/invoices over various of channels, e.g. slack, email.
- reconcline invoices with a business account to mark that money is received and invoice is paid.
- tax reporting - once a year we need to sign and send special report to the tax services.

## MVP
As a first thing, I've crafted a [cli application](https://github.com/timemate/toggl-sync), which can be executed on a developer's computer and in the background do a very simple thing:
- fetch data from Toggle
- synchronize it with Jira.

### What's next? 
[Publishing](https://github.com/timemate/homebrew-tap) a golang application to [homebrew](https://brew.sh), so anyone can use it.

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

**Updating the Protocol**
If you update the protocol buffers file, you can regenerate the file using the following command.

```shell
protoc \
  --go_out=. \
  --go_opt=paths=source_relative \
  --go-grpc_out=. \
  --go-grpc_opt=paths=source_relative \
  ./proto/tracker.proto
```

## Plugins in Golang

### Compile-time plugins
Compile-time plugins consist of code packages that get compiled into the application's main binary. Once the binary is built, its functionality is fixed.

The best known example of a compile-time plugin system in Go is drivers for the `database/sql` package.
Usually Discovery and Registration of such plugins happens automatically, when you `import` a package.

### Run-time plugins
Run-time plugins consist of code that does not get compiled into the original binary of the main application; instead, it hooks up to this application at run-time. In compiled languages, a common tool to achieve this goal is *shared libraries*(.so), and Go supports this approach as well.

Go comes with a plugin package built into the standard library. This package lets us write Go programs that get compiled into shared libraries instead of into executable binaries; further, it provides simple functions for loading shared libraries and getting symbols from them.

Discovery and Registration: accomplished via file system lookup.

File `./lang/eng.so`
```go
package main

import "fmt"

type greeting string

func (g greeting) Greet() {
    fmt.Println("Hello Universe")
}

// exported as symbol named "Greeter"
var Greeter greeting
```

Building the plugin:
```shell
go build -buildmode=plugin -o eng/eng.so eng/greeter.go
```

And usage of the plugin:
```go
package main

import "plugin";

type Greeter interface {
	Greet()
}

func main() {
	// determine plugin to load
	mod := "./lang/eng.so"

	// load module
	// 1. open the so file to load the symbols
	plug, err := plugin.Open(mod)
	if err != nil {
		fmt.Println(err)
		os.Exit(1)
	}

	// 2. look up a symbol (an exported function or variable)
	// in this case, variable Greeter
	symGreeter, err := plug.Lookup("Greeter")
	if err != nil {
		fmt.Println(err)
		os.Exit(1)
	}

	// 3. Assert that loaded symbol is of a desired type
	// in this case interface type Greeter (defined above)
	var greeter Greeter
	greeter, ok := symGreeter.(Greeter)
	if !ok {
		fmt.Println("unexpected type from module symbol")
		os.Exit(1)
	}

	// 4. use the module
	greeter.Greet()

}
```

However, this approach also has some serious downsides. The most important downside is that Go is very picky about keeping the main application and the shared libraries it loads compatible.

#### Alternatives?

RPC-based plugin systems!

[hashicorp/go-plugin](https://github.com/hashicorp/go-plugin) is one of the most popular.

It has several important upsides:

- Isolation: crash in a plugin does not bring the whole application down.
- Interoperability between languages: if RPC is the interface, do you care what language the plugin is written in?
- Distribution: if plugins interface via the network, we can easily distribute them to run on different machines for gains in performance, reliability, and so on.

Let's take a look into a [code example](https://github.com/hashicorp/go-plugin/tree/master/examples/grpc)

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
