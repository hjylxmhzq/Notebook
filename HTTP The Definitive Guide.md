### HTTP The Definitive Guide

#### http methods

Http supports several different request command to tell server to perform an actual action

Here lists five common http methods

- GET: get named resource from server
- PUT: store data into a named server resource
- DELETE: delete the named resource from server
- POST: send data into a server gateway application
- HEAD: send just http headers

#### status codes

A three-digit code to tell client if the request is succeeded or another action is required

Here lists the descriptions of status codes

- 200: OK, data response correctly
- 302: Redirect, to get resource from another place
- 404: Can not find the resource

#### http message

Http message is line-oriented sequences of characters, and they are plain text so is easy to read and write, there are two kinds of http messages, request message, response message

http message consists of three parts:

- start line: indicating what to do for a request or what happened for a response, like http methods used in 

#### TCP/IP

TCP is Transmission Control Protocol, the popular reliable Internet transport protocol

and http is a application layer protocol which doesn't worry about the nitty-gritty details of network communication

TCP can provide:

- Error-free transportation
- In-order delivery
- Unsegment data stream

#### protocol versions

- http/0.9
- http/1.0
- http/1.0+
- http/1.1
- http/2.0
- http-NG

#### Architectural Components of the Web

##### proxy

Intermediaries on the website, sit between a client and a server

##### cache

A special type of proxies that keep copies of popular documents that pass through the proxy

##### gateway

A special kind of server that act as intermediaries for other server. usually used to convert http protocol to other protocol

##### tunnel

Http tunnel are often used to transport non-http data via over one or more http connections

##### agent

Or user agent, are client programs that make http request on the user's behalf like web browsers, any application can issue http requests is an http agent

