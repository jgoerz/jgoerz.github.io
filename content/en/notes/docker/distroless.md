---
title: "Distroless"
linkTitle: "Distroless"
description: >
  Smaller, more secure containers using distroless from Google.
---
<!-- date: 2023-03-17T00:12:00-05:00 -->

## Go

```docker
FROM golang:1.20 as builder

WORKDIR /app
COPY . .
RUN CGO_ENABLED=0 go build -o main .

# https://github.com/GoogleContainerTools/distroless#examples-with-docker
FROM gcr.io/distroless/cc-debian11
WORKDIR /app
COPY --from=builder /app/main .

EXPOSE 8080
CMD ["/app/main"]
```

## C, Java, Node.js, Python, Rust

[Distroless Github Repository](https://github.com/GoogleContainerTools/distroless#examples-with-docker)

## Elixir

- [bitwalker/alpine-erlang](https://github.com/bitwalker/alpine-erlang)
- [Discussion about adding elixir to erlang container](https://elixirforum.com/t/smallest-docker-container-with-elixir-app/6975)

## RedHat Based Images

The distroless images use [Debian](https://www.debian.org).   Here's some
documentation that shows how to use [RedHat](https://www.redhat.com) based images.

[Documentation link.](https://catalog.redhat.com/software/containers/ubi8-micro/601a84aadd19c7786c47c8ea?container-tabs=overview)

## References

<!-- Format for online resources: -->
<!-- Author Last Name, First Name. “Title of Work.” Title of Site, Sponsor or -->
<!-- Publisher (include only if different from website title or author), Date of -->
<!-- Publication or Update Date, URL. Accessed Date (only if no date of publication -->
<!-- or update date). -->

1. Velichko, Ivan. "What's inside of a Distroless Container Image: Taking a Deeper Look." March 17, 2023 [URL](https://iximiuz.com/en/posts/containers-distroless-images)
1. Moore, Matthew. "Distroless Docker: Containerizing Apps, not VMs." swampUP 2017. [URL](https://swampup2017.sched.com/event/A6CW/distroless-docker-containerizing-apps-not-vms),  [Video](https://www.youtube.com/watch?v=lviLZFciDv4)
1. Rehn, Adam.  "Identifying application runtime dependencies."  Unreal Containers. March 17, 2023. [URL](https://unrealcontainers.com/blog/identifying-application-runtime-dependencies)
1. Deshpande, Tanmay.  "Which Container Images To Use - Distroless or Alpine?" June 11, 2021.  [URL](https://itnext.io/which-container-images-to-use-distroless-or-alpine-96e3dab43a22)
