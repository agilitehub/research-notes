# HTTP2
> Common Buzz Words, descriptions and methodologies of HTTP2

## Table of Contents
- [What is HTTP2](#what-is-http2)
- [What makes it different from HTTP](#what-makes-it-different-from-http)
- [Multiplexing](#multiplexing)
- [Server Pushing](#server-pushing)
- [Header Compression](#header-compression)
- [Buzz Words](#buzz-words)

## What is HTTP2
HTTP2 is the next version of HTTP and is based on Google's SPDY Protocol (originally designed to speed up serving of web pages)

It is important to note that HTTP2 is not a replacement for HTTP. It is merely an extension, with all the core concepts such as HTTP methods, status codes and header fields remaining the same.

## What makes it different from HTTP
It is binary instead of textual, it is fully multiplexed instead of ordered blocking, it uses header compression to reduce overhead, it allows for server pushing to add responses proactively into the browser cache.

## Multiplexing
Mulitplexing is a method by which multiple signals are combined into one signal over a shared medium, the aim is to share a scarce resource. Mutiplexing reduces latency of requests and responses, it allows browsers to include multiple requests in a single TCP connection which in turn enables browsers to request all the assets in parallel.

## Server Pushing
HTTP2 Server Pushing allows and HTTP2 compliant server to send resources to the HTTP2 compliant client before the client requests them, It is for the most part a performance technique that can be helpful in loading resources.

## Header Compression
All HTTP/1.1 requests have to have headers which typically duplicate the same info, while HTTP2 forces all headers to be sent in a compressed format.

## Buzz Words
- Server Push
- Multiplexing
- Binary
- Header Compression
- Reduced Latency
- Performance
- Load times
- Stream Priority



