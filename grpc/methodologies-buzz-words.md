# Methodologies & Buzz Words
> Common Buzz Words, descriptions and methodologies of gRPC

## Table of Contents
- [What is gRPC](#what-is-grpc)
- [What is a RPC](#what-is-a-rpc)
- [What does a gRPC stand for](#what-does-grpc-stand-for)
- [What is Protocol Buffers](#what-is-protocol-buffers)
- [Buzz Words](#buzz-words)

## What is gRPC
gRPC is an open source remote procedure call (RPC) system initially developed at Google. 
It uses HTTP/2 for transport, Protocol Buffers as the interface description language, and provides features such as authentication, bidirectional streaming and flow control, blocking or nonblocking bindings, and cancellation and timeouts.

## What is a RPC (Remote Procedure Call)
A RPC is a Procedure that can be executed as if it was local (Same Location as Project) but the procedure actually exists on another remote location.

In your client code it will look as if you're calling a function directly on the server but instead your are callling a function over the network.

## What does gRPC stand for
Some sources state that the abbreviation gRPC stands for Google Remote Procedure Call but it doesn't, gRPC is recursive acronym which means `gRPC Remote Procedure Call`

## What is Protocol Buffers (Protobufs)
Protocol Buffers is a method of serializing structured data. It is useful in developing programs to communicate with each other over a wire or for storing data.

`Serialization in the context of data storage is the process of translating data structures or object state into a format that can be stored for example, in a file or memory buffer`

Why would you want to use Protobufs? Code can be generated for any language, data is binary and efficiently serialised (small payloads), very convenient for transporting a lot of data and allows for easy API evolution using rules.

One .proto file works for over 12 languages (server and client) and allows you to use a framework that scales to millions of RPCs per second.

Protobufs are basically Schemas for your data, it is lightweight, takes up less space, faster transmission, validation of data structure, easy to modify if data format ever has to change.

## Buzz Words
- Modern
- Fast
- Efficient
- Built on HTTP2
- Low Latency
- Streaming
- Bidirectional Streaming
- Language Independent
- Protocol Buffers (Protobufs)



