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

# Next Steps

- Build first gRPC server/client
- Let's implement couple of services on each side to see the whole picture
- Prepare timemate app for publication to [homebrew](https://brew.sh/)

## Resources

- [GRPC](https://grpc.io/docs/what-is-grpc/introduction/)
- [FigmaJam](https://www.figma.com/figjam/)

## Q/A

Any questions?
