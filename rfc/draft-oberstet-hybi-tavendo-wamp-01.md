% Title = "Web Application Messaging Protocol - Basic Profile"
% abbrev = "Web Application Messaging Protocol - Basic Profile"
% category = "std"
% docName = "draft-oberstet-hybi-tavendo-wamp-01"
% ipr= "trust200902"
% area = "Applications and Real-Time (art)"
% workgroup = "BiDirectional or Server-Initiated HTTP"
% keyword = ["WebSocket, WAMP, real-time, RPC, PubSub"]
%
% date = 2015-09-17T00:00:00Z
%
% [pi]
% toc = "yes"
%
% #Independent Submission
% [[author]]
% initials="T.O."
% surname="Oberstein"
% fullname="Tobias G. Oberstein"
% organization = "Tavendo GmbH"
%   [author.address]
%   email = "tobias.oberstein@tavendo.de"
%   

.# Abstract

This document defines the basic profile for the Web Application Messaging Protocol (WAMP). WAMP is a routed protocol which provides two messaging patterns: Publish & Subscribe and routed Remote Procedure Calls. It is intended to connect application components in distributed applications. WAMP uses WebSocket as its default transport, but can be transmitted via any other protocol which allows for ordered, reliable, bi-directional and message-based communication.

{mainmatter}



# Introduction

## Background

_This section is non-normative._


-- write me --

* distributed applications the new norm
* realtime a requirement in many cases
* WebSocket a protocol which includes the browser in this
* WS is low-level
* a lot of protocols which implement PubSub on top
* quick PubSub overview
* this does not natively cover the full range of what application components want to communicate
* there is also a need to call procedures and receive results
* the common model for RPCs is a client-server connection. in distributed applications the need for each caller to be aware of the callee's identity and to establish a connection presents at least significant overhead, at worst makes connections impossible (NAT problem)
* WAMP provides routed RPCs in the same protocol as PubSub
* this enables all application messaging to run over a single protocol using native messaging patterns

## Protocol Overview

_This section is non-normative._

This document defines the basic profile of WAMP. The basic profile 

## Desing Philosophy

_This section is non-normative._

### Application Code

WAMP is designed for application code to run inside *Clients*, i.e. *Peers* of the roles *Callee*, *Caller*, *Publisher* and *Subscriber*.

*Routers*, i.e. *Peers* of the roles *Brokers* and *Dealers* are responsible for **generic call and event routing** and do not run application code.

This allows to transparently exchange *Broker* and *Dealer* implementations without affecting the application and to distribute and deploy application components flexibly:

-- adapt figure/appcode.png ---

> Note that a **program** that implements e.g. the *Dealer* role might at the same time implement e.g. a built-in *Callee*. It is the *Dealer* and *Broker* that are generic, not the program.
>

### Router Implementation Specifics

Specific WAMP *Broker* and *Dealer* implementations might differ in aspects such as:

* support for WAMP Advanced Profile
* router networks (clustering and federation)
* authentication and authorization schemes
* message persistence
* management and monitoring

The definition and documentation of implementation specific *Router* features like above is outside the scope of this document.

## Security Model

_This section is non-normative._



# Conformance Requirements

## Terminology and Other Conventions

The keywords **MUST**, **MUST NOT**, **REQUIRED**, **SHALL**, **SHALL NOT**, **SHOULD**,
**SHOULD NOT**, **RECOMMENDED**, **MAY**, and **OPTIONAL**, when they appear in this
document, are to be interpreted as described in [@?RFC2119].



# Realms, Sessions and Transports

A *Realm* is a WAMP routing and administrative domain (optionally) protected by authentication and authorization.
A *Session* is a transient conversation between two *Peers* attached to a *Realm* and running over a *Transport*.

-- adapt sessions2.npg --

A *Transport* connects two WAMP *Peers* and provides a channel over which WAMP messages for a WAMP *Session* can flow in both directions.

WAMP can run over different *transports*. A *Transport* suitable for WAMP must have the following characteristics:

* message-based
* bidirectional
* reliable
* ordered

The default transport for WAMP is [WebSocket](http://tools.ietf.org/html/rfc6455), where WAMP is an [officially registered](http://www.iana.org/assignments/websocket/websocket.xml) subprotocol. Other transports may be defined in a subsequent WAMP Advanced Profile document.

WAMP is currently defined for the following *serializations*:

* JSON
* MsgPack

When the transport allows for - as is the case with WebSocket - WAMP lets you combine the transport with any serialization.



# Peers and Roles

A WAMP *Session* connects two *Peers*, a *Client* and a *Router*. Each WAMP *Peer* can implement one or more roles.

A *Client* can implement any combination of the *Roles*:

 * *Callee*
 * *Caller*
 * *Publisher*
 * *Subscriber*

and a *Router* can implement either or both of the *Roles*:

 * *Dealer*
 * *Broker*

> This document describes WAMP as in client-to-router communication. Direct client-to-client communication is not supported by WAMP. Router-to-router communication MAY be defined by a specific router implementation.
>


## Symmetric Messaging

It is important to note that though the establishment of a *Transport* might have a inherent asymmetry (like a TCP client establishing a WebSocket connection to a server), and *Clients* establish WAMP sessions by attaching to *Realms* on *Routers*, WAMP itself is designed to be fully symmetric for application components.

After the transport and a session have been established, any application component may act as *Caller*, *Callee*, *Publisher* and *Subscriber* at the same time. And *Routers* provide the fabric on top of which WAMP runs a symmetric application messaging service.


## Remote Procedure Call Roles

The Remote Procedure Call messaging pattern involves peers of three different roles:

* *Callee (Client)*
* *Caller (Client)*
* *Dealer (Router)*

A *Caller* issues calls to remote procedures by providing the procedure URI and any arguments for the call.
The *Callee* will execute the procedure using the supplied arguments to the call and return the result of the call to the *Caller*.

*Callees* register procedures they provide with *Dealers*. *Callers* initiate procedure calls first to *Dealers*. *Dealers* route calls incoming from *Callers* to *Callees* implementing the procedure called, and route call results back from *Callees* to *Callers*.

The *Caller* and *Callee* will usually run application code, while the *Dealer* works as a generic router for remote procedure calls decoupling *Callers* and *Callees*.


## Publish & Subscribe Roles

The Publish & Subscribe messaging pattern involves peers of three different roles:

* *Subscriber (Client)*
* *Publisher (Client)*
* *Broker (Router)*

A *Publishers* publishes events to topics by providing the topic URI and any payload for the event. *Subscribers* of the topic will receive the event together with the event payload.

*Subscribers* subscribe to topics they are interested in with *Brokers*. *Publishers* initiate publication first at *Brokers*. *Brokers* route events incoming from *Publishers* to *Subscribers* that are subscribed to respective topics.

The *Publisher* and *Subscriber* will usually run application code, while the *Broker* works as a generic router for events decoupling *Publishers* from *Subscribers*.


## Peers with multiple Roles

Note that *Peers* might implement more than one role: e.g. a *Peer* might act as *Caller*, *Publisher* and *Subscriber* at the same time. Another *Peer* might act as both a *Broker* and a *Dealer*.



# Building Blocks

WAMP is defined with respect to the following building blocks

1.  Identifiers
2.  Serializations
3.  Transports

For each building block, WAMP only assumes a defined set of requirements, which allows to run WAMP variants with different concrete bindings.


## Identifiers

### URIs {#uris}

WAMP needs to identify the following *persistent* resources:

1.  Topics
2.  Procedures
3.  Errors

These are identified in WAMP using *Uniform Resource Identifiers* (URIs) that MUST be Unicode strings.

> Note: When using JSON as WAMP serialization format, URIs (as other strings) are transmitted in UTF-8 encoding.

*Examples*

* `com.myapp.mytopic1`
* `com.myapp.myprocedure1`
* `com.myapp.myerror1`

The URIs are understood to form a single, global, hierarchical namespace for WAMP.

> The namespace is unified for topics, procedures and errors - these different resource types do NOT have separate namespaces.
>

To avoid resource naming conflicts, we follow the package naming convention from Java where URIs SHOULD begin with (reversed) domain names owned by the organization defining the URI.

#### Relaxed/Loose URIs

URI components (the parts between two `.`s, the head part up to the first `.`, the tail part after the last `.`) MUST NOT contain a `.`, `#` or whitespace characters and MUST NOT be empty (zero-length strings).

> The restriction not to allow `.` in component strings is due to the fact thate `.` is used to separate components, and WAMP associates semantics with resource hierarchies, such as in pattern-based subscriptions which may be part of an Advanced Profile. The restriction not to allow empty (zero-length) strings as components is due to the fact that this may be used to denote wildcard components with pattern-based subscriptions and registrations in an Advanced Profile. The character `#` is not allowed since this is reserved for internal use by *Dealers* and *Brokers*.

As an example, the following regular expression could be used in Python to check URIs according to above rules:

{align="left"}
``` python
    ## loose URI check disallowing empty URI components
    pattern = re.compile(r"^([^\s\.#]+\.)*([^\s\.#]+)$")
```

When empty URI components are allowed (which may the case for specific messages which are part of an Advanced Profile), you can use this regular expression:

{align="left"}
``` python
    ## loose URI check allowing empty URI components
    pattern = re.compile(r"^(([^\s\.#]+\.)|\.)*([^\s\.#]+)?$")
```

#### Strict URIs

While the above rules MUST be followed, following a stricter URI rule is recommeded: URI components SHOULD only contain letters, digits and `_`.

As an example, the following regular expression could be used in Python to check URIs according to the above rules:

{align="left"}
```python
    ## strict URI check disallowing empty URI components
    pattern = re.compile(r"^([0-9a-z_]+\.)*([0-9a-z_]+)$")
```

When empty URI components are allowed (which may the case for specific messages which are part of an Advanced Profile), you can use this regular expression:

{align="left"}
```python
    ## strict URI check allowing empty URI components
    pattern = re.compile(r"^(([0-9a-z_]+\.)|\.)*([0-9a-z_]+)?$")
```

> Following the suggested regular expression will make URI components valid identifiers in most languages (modulo URIs starting with a digit and language keywords) and the use of lower-case only will make those identifiers unique in languages that have case-insensitive identifiers. Following this suggestion can allow implementations to map topics, procedures and errors to the language environment in a completely transparent way.

#### Reserved URIs

Further, application URIs MUST NOT use `wamp` as a first URI component, since this is reserved for URIs predefined with the WAMP protocol itself.

*Examples*

* `wamp.error.not_authorized`
* `wamp.error.procedure_already_exists`


### IDs {#ids}

WAMP needs to identify the following ephemeral entities each in the scope noted:

1. Sessions (*global scope*)
2. Publications (*global scope*)
3. Subscriptions (*router scope*)
4. Registrations (*router scope*)
5. Requests (*session scope*)

These are identified in WAMP using IDs that are integers between (inclusive) **0** and **2^53** (9007199254740992):

* IDs in the *global scope* MUST be drawn *randomly* from a *uniform distribution* over the complete range [0, 2^53]
* IDs in the *router scope* can be chosen freely by the specific router implementation
* IDs in the *session scope* SHOULD be incremented by 1 beginning with 1 (for each direction - *Client-to-Router* and *Router-to-Client*)

> The reason to choose the specific upper bound is that 2^53 is the largest integer such that this integer and *all* (positive) smaller integers can be represented exactly in IEEE-754 doubles. Some languages (e.g. JavaScript) use doubles as their sole number type. Most languages do have signed and unsigned 64-bit integer types which both can hold any value from the specified range.
>

The following is a complete list of usage of IDs in the three categories for all WAMP messages. For a full definition of these see [messages section](#messages).

#### Global Scope IDs

* `WELCOME.Session`
* `PUBLISHED.Publication`
* `EVENT.Publication`


#### Router Scope IDs

* `EVENT.Subscription`
* `SUBSCRIBED.Subscription`
* `REGISTERED.Registration`
* `UNSUBSCRIBE.Subscription`
* `UNREGISTER.Registration`
* `INVOCATION.Registration`


#### Session Scope IDs

* `ERROR.Request`
* `PUBLISH.Request`
* `PUBLISHED.Request`
* `SUBSCRIBE.Request`
* `SUBSCRIBED.Request`
* `UNSUBSCRIBE.Request`
* `UNSUBSCRIBED.Request`
* `CALL.Request`
* `CANCEL.Request`
* `RESULT.Request`
* `REGISTER.Request`
* `REGISTERED.Request`
* `UNREGISTER.Request`
* `UNREGISTERED.Request`
* `INVOCATION.Request`
* `INTERRUPT.Request`
* `YIELD.Request`



## Serializations

WAMP is a message based protocol that requires serialization of messages to octet sequences to be sent out on the wire.

A message *serialization* format is assumed that (at least) provides the following types:

* `integer` (non-negative)
* `string` (UTF-8 encoded Unicode)
* `bool`
* `list`
* `dict` (with string keys)

> WAMP *itself* only uses the above types, e.g. it does not use the JSON data types `number` (non-integer) and `null`. The *application payloads* transmitted by WAMP (e.g. in call arguments or event payloads) may use other types a concrete serialization format supports.
>

WAMP defines two bindings for message *serialization*:

1. [JSON](http://www.json.org/)
2. [MsgPack](http://msgpack.org/)

Other bindings for *serialization* may be defined in future WAMP versions.

### JSON

With JSON serialization, each WAMP message is serialized according to the JSON specification as described in [RFC4627](http://www.ietf.org/rfc/rfc4627.txt).

Further, binary data follows a convention for conversion to JSON strings. For details see the Appendix.

### MsgPack

With MsgPack serialization, each WAMP message is serialized according to the [MsgPack specification](https://github.com/msgpack/msgpack/blob/master/spec.md).

> The **version 5 or later** of MsgPack MUST BE used, since this version is able to differentiate between strings and binary values.



## Transports

WAMP assumes a *transport* with the following characteristics:

  1. message-based
  2. reliable
  3. ordered
  4. bidirectional (full-duplex)


### WebSocket Transport

The default transport binding for WAMP is [WebSocket](http://tools.ietf.org/html/rfc6455).

With WebSocket in **unbatched mode**, WAMP messages are transmitted as WebSocket messages: each WAMP message is transmitted as a separate WebSocket message (not WebSocket frame).

The WAMP protocol MUST BE negotiated during the WebSocket opening handshake between *Peers* using the WebSocket subprotocol negotiation mechanism.

WAMP uses the following WebSocket subprotocol identifiers for unbatched modes:

* `wamp.2.json`
* `wamp.2.msgpack`

With `wamp.2.json`, *all* WebSocket messages MUST BE of type **text** (UTF8 encoded payload) and use the JSON message serialization.

With `wamp.2.msgpack`, *all* WebSocket messages MUST BE of type **binary** and use the MsgPack message serialization.

### Transport and Session Lifetime

The following sequence diagram shows the relation between the *lifetime* of a WebSocket transport and WAMP sessions:

-- add adaptation of figure/sessions4.png --
-- add description - diagrams as normative are frowned upon --


# Messages {#messages}

All WAMP messages are of the same structure, a `list` with a first element `MessageType` followed by one or more message type specific elements:

{align="left"}
        [MessageType|integer, ... one or more message type specific 
            elements ...]

The notation `Element|type` denotes a message element named `Element` of type `type`, where `type` is one of

* `uri`: a string URI as defined in [URIs](#uris)
* `id`: an integer ID as defined in [IDs](#ids)

or

* `integer`: a non-negative integer
* `string`: a Unicode string, including the empty string
* `bool`: a boolean value (`true` or `false`) - integers MUST NOT be used instead of boolean value
* `dict`: a dictionary (map) where keys MUST be strings, keys MUST be uniueand serialization order is undefined (left to the serializer being used)
* `list`: a list (array) where items can be again any of this enumeration

*Example*

A `SUBSCRIBE` message has the following format

{align="left"}
        [SUBSCRIBE, Request|id, Options|dict, Topic|uri]

Here is an example message conforming to above format

{align="left"}
        [32, 713845233, {}, "com.myapp.mytopic1"]


## Extensibility

Some WAMP messages contain `Options|dict` or `Details|dict` elements. This allows for future extensibility and implementations that only provide subsets of functionality by ignoring unimplemented attributes. Keys in `Options` and `Details` MUST be of type `string` and MUST match the regular expression `[a-z][a-z0-9_]{2,}` for WAMP *predefined* keys. Implementations MAY use implementation-specific keys which MUST match the regular expression `_[a-z0-9_]{3,}`. Attributes unknown to an implementation MUST be ignored.


## Polymorphism

For a given `MessageType` *and* number of message elements the expected types are uniquely defined. Hence there are no polymorphic messages in WAMP. This leads to a message parsing and validation control flow that is efficient, simple to implement and simple to code for rigorous message format checking.


## Structure

The *application* payload (that is call arguments, call results, event payload etc) is always at the end of the message element list. The rationale is: *Brokers* and *Dealers* have no need to inspect (parse) the application payload. Their business is call/event routing. Having the application payload at the end of the list allows *Brokers* and *Dealers* to skip parsing it altogether. This can improve efficiency and performance.


## Message Definitions

WAMP defines the following messages which are explained in detail in the following sections.

The messages concerning the WAMP session itself are mandatory for all *Peers*, i.e. a *Client* MUST implement `HELLO`, `ABORT` and `GOODBYE`, while a *Router* MUST implement `WELCOME`, `ABORT` and `GOODBYE`.

All other messages are mandatory *per role*, i.e. in an implementation which only provides a *Client* with the role of *Publisher* MUST additionally implement sending `PUBLISH` and receiving `PUBLISHED` and `ERROR` messages.

### Session Lifecycle

#### HELLO

Sent by a *Client* to initiate opening of a WAMP session to a *Router* attaching to a *Realm*.

{align="left"}
        [HELLO, Realm|uri, Details|dict]

#### WELCOME

Sent by a *Router* to accept a *Client*. The WAMP session is now open.

{align="left"}
        [WELCOME, Session|id, Details|dict]

#### ABORT

Sent by a *Peer* to abort the opening of a WAMP session. No response is expected.

{align="left"}
      [ABORT, Details|dict, Reason|uri]

#### GOODBYE

Sent by a *Peer* to close a previously opened WAMP session. Must be echo'ed by the receiving *Peer*.

{align="left"}
        [GOODBYE, Details|dict, Reason|uri]

#### ERROR

Error reply sent by a *Peer* as an error response to different kinds of requests.

{align="left"}
        [ERROR, REQUEST.Type|int, REQUEST.Request|id, Details|dict, 
            Error|uri] 

        [ERROR, REQUEST.Type|int, REQUEST.Request|id, Details|dict, 
            Error|uri, Arguments|list] 

        [ERROR, REQUEST.Type|int, REQUEST.Request|id, Details|dict, 
            Error|uri, Arguments|list, ArgumentsKw|dict]


### Publish & Subscribe

#### PUBLISH

Sent by a *Publisher* to a *Broker* to publish an event.

{align="left"}
        [PUBLISH, Request|id, Options|dict, Topic|uri]

        [PUBLISH, Request|id, Options|dict, Topic|uri, 
            Arguments|list]

        [PUBLISH, Request|id, Options|dict, Topic|uri, 
            Arguments|list, ArgumentsKw|dict]

#### PUBLISHED

Acknowledge sent by a *Broker* to a *Publisher* for acknowledged publications.

{align="left"}
        [PUBLISHED, PUBLISH.Request|id, Publication|id]

#### SUBSCRIBE

Subscribe request sent by a *Subscriber* to a *Broker* to subscribe to a topic.

{align="left"}
        [SUBSCRIBE, Request|id, Options|dict, Topic|uri]

#### SUBSCRIBED

Acknowledge sent by a *Broker* to a *Subscriber* to acknowledge a subscription.

{align="left"}
        [SUBSCRIBED, SUBSCRIBE.Request|id, Subscription|id]

#### UNSUBSCRIBE

Unsubscribe request sent by a *Subscriber* to a *Broker* to unsubscribe a subscription.

{align="left"}
        [UNSUBSCRIBE, Request|id, SUBSCRIBED.Subscription|id]

#### UNSUBSCRIBED

Acknowledge sent by a *Broker* to a *Subscriber* to acknowledge unsubscription.

{align="left"}
        [UNSUBSCRIBED, UNSUBSCRIBE.Request|id]

#### EVENT

Event dispatched by *Broker* to *Subscribers* for subscription the event was matching.

{align="left"}
        [EVENT, SUBSCRIBED.Subscription|id, PUBLISHED.Publication|id,
            Details|dict]
        
        [EVENT, SUBSCRIBED.Subscription|id, PUBLISHED.Publication|id, 
            Details|dict, PUBLISH.Arguments|list]
        
        [EVENT, SUBSCRIBED.Subscription|id, PUBLISHED.Publication|id, 
            Details|dict, PUBLISH.Arguments|list, 
            PUBLISH.ArgumentsKw|dict]

> An event is dispatched to a *Subscriber* for a given `Subscription|id` *only once*. On the other hand, a *Subscriber* that holds subscriptions with different `Subscription|id`s that all match a given event will receive the event on each matching subscription.
>

### Routed Remote Procedure Calls

#### CALL

Call as originally issued by the *Caller* to the *Dealer*.

{align="left"}
      [CALL, Request|id, Options|dict, Procedure|uri]

      [CALL, Request|id, Options|dict, Procedure|uri, Arguments|list]

      [CALL, Request|id, Options|dict, Procedure|uri, Arguments|list,
          ArgumentsKw|dict]

#### RESULT

Result of a call as returned by *Dealer* to *Caller*.

{align="left"}
        [RESULT, CALL.Request|id, Details|dict]
        
        [RESULT, CALL.Request|id, Details|dict, YIELD.Arguments|list]
        
        [RESULT, CALL.Request|id, Details|dict, YIELD.Arguments|list, 
            YIELD.ArgumentsKw|dict]

#### REGISTER

A *Callees* request to register an endpoint at a *Dealer*.

{align="left"}
        [REGISTER, Request|id, Options|dict, Procedure|uri]

#### REGISTERED

Acknowledge sent by a *Dealer* to a *Callee* for successful registration.

{align="left"}
        [REGISTERED, REGISTER.Request|id, Registration|id]

#### UNREGISTER

A *Callees* request to unregister a previously established registration.

{align="left"}
        [UNREGISTER, Request|id, REGISTERED.Registration|id]

#### UNREGISTERED

Acknowledge sent by a *Dealer* to a *Callee* for successful unregistration.

{align="left"}
        [UNREGISTERED, UNREGISTER.Request|id]

#### INVOCATION

Actual invocation of an endpoint sent by *Dealer* to a *Callee*.

{align="left"}
        [INVOCATION, Request|id, REGISTERED.Registration|id, 
            Details|dict]

        [INVOCATION, Request|id, REGISTERED.Registration|id, 
            Details|dict, C* Arguments|list]

        [INVOCATION, Request|id, REGISTERED.Registration|id, 
            Details|dict, CALL.Arguments|list, CALL.ArgumentsKw|dict]

#### YIELD

Actual yield from an endpoint sent by a *Callee* to *Dealer*.

{align="left"}
        [YIELD, INVOCATION.Request|id, Options|dict]

        [YIELD, INVOCATION.Request|id, Options|dict, Arguments|list]

        [YIELD, INVOCATION.Request|id, Options|dict, Arguments|list,
            ArgumentsKw|dict]

## Message Codes and Direction

The following table lists the message type code for **all 25 messages defined in WAMP** and their direction between peer roles.

> "Tx" indicates the message is sent by the respective role, and "Rx" indicates the message is received by the respective role.

| Cod | Message        |  Pub |  Brk | Subs |  Calr | Dealr | Callee|
|-----|----------------|------|------|------|-------|-------|-------|
|  1  | `HELLO`        | Tx   | Rx   | Tx   | Tx    | Rx    | Tx    |
|  2  | `WELCOME`      | Rx   | Tx   | Rx   | Rx    | Tx    | Rx    |
|  3  | `ABORT`        | Rx   | TxRx | Rx   | Rx    | TxRx  | Rx    |
|  6  | `GOODBYE`      | TxRx | TxRx | TxRx | TxRx  | TxRx  | TxRx  |
|     |                |      |      |      |       |       |       |
|  8  | `ERROR`        | Rx   | Tx   | Rx   | Rx    | TxRx  | TxRx  |
|     |                |      |      |      |       |       |       |
| 16  | `PUBLISH`      | Tx   | Rx   |      |       |       |       |
| 17  | `PUBLISHED`    | Rx   | Tx   |      |       |       |       |
|     |                |      |      |      |       |       |       |
| 32  | `SUBSCRIBE`    |      | Rx   | Tx   |       |       |       |
| 33  | `SUBSCRIBED`   |      | Tx   | Rx   |       |       |       |
| 34  | `UNSUBSCRIBE`  |      | Rx   | Tx   |       |       |       |
| 35  | `UNSUBSCRIBED` |      | Tx   | Rx   |       |       |       |
| 36  | `EVENT`        |      | Tx   | Rx   |       |       |       |
|     |                |      |      |      |       |       |       |
| 48  | `CALL`         |      |      |      | Tx    | Rx    |       |
| 50  | `RESULT`       |      |      |      | Rx    | Tx    |       |
|     |                |      |      |      |       |       |       |
| 64  | `REGISTER`     |      |      |      |       | Rx    | Tx    |
| 65  | `REGISTERED`   |      |      |      |       | Tx    | Rx    |
| 66  | `UNREGISTER`   |      |      |      |       | Rx    | Tx    |
| 67  | `UNREGISTERED` |      |      |      |       | Tx    | Rx    |
| 68  | `INVOCATION`   |      |      |      |       | Tx    | Rx    |
| 70  | `YIELD`        |      |      |      |       | Rx    | Tx    |


## Extension Messages

WAMP uses type codes from the core range [0, 255]. Implementations MAY define and use implementation specific messages with message type codes from the extension message range [256, 1023]. For example, a router MAY implement router-to-router communication by using extension messages.

## Empty Arguments and Keyword Arguments

Implementations SHOULD avoid sending empty `Arguments` lists.

E.g. a `CALL` message

{align="left"}
        [CALL, Request|id, Options|dict, Procedure|uri, 
            Arguments|list]

where `Arguments == []` SHOULD be avoided, and instead

{align="left"}
        [CALL, Request|id, Options|dict, Procedure|uri]

SHOULD be sent.

Implementations SHOULD avoid sending empty `ArgumentsKw` dicts.

E.g. a `CALL` message

{align="left"}
        [CALL, Request|id, Options|dict, Procedure|uri, 
            Arguments|list, ArgumentsKw|dict]

where `ArgumentsKw == {}` SHOULD be avoided, and instead

{align="left"}
        [CALL, Request|id, Options|dict, Procedure|uri, 
            Arguments|list]

SHOULD be sent when `Arguments` is non-empty and

{align="left"}
        [CALL, Request|id, Options|dict, Procedure|uri]

SHOULD be sent when `Arguments` is empty.



# Sessions

The message flow between *Clients* and *Routers* for opening and closing WAMP sessions involves the following messages:

1. `HELLO`
2. `WELCOME`
3. `ABORT`
4. `GOODBYE`

## Session Establishment

### HELLO

After the underlying transport has been established, opening of a WAMP session is initiated by the *Client* sending a `HELLO` message to the *Router*

{align="left"}
        [HELLO, Realm|uri, Details|dict]

where

* `Realm` is a string identifying the realm this session should attach to
* `Details` is a dictionary that allows to provide additional opening information (see below).

The `HELLO` message MUST be the very first message sent by the *Client* after the transport has been established.

In the WAMP Basic Profile without session authentication the *Router* will reply with a `WELCOME` or `ABORT` message.

-- adapt figure/hello.png

A WAMP session starts its lifetime when the *Router* has sent a `WELCOME` message to the *Client*, and ends when the underlying transport closes or when the session is closed explicitly by either peer sending the `GOODBYE` message (see below).

It is a protocol error to receive a second `HELLO` message during the lifetime of the session and the *Peer* must fail the session if that happens.

#### Client: Role and Feature Announcement

WAMP uses *Role & Feature announcement* instead of *protocol versioning* to allow

* implementations only supporting subsets of functionality
* future extensibility

A *Client* must announce the **roles** it supports via `Hello.Details.roles|dict`, with a key mapping to a `Hello.Details.roles.<role>|dict` where `<role>` can be:

* `publisher`
* `subscriber`
* `caller`
* `callee`

A *Client* can support any combination of above roles but must support at least one role.

The `<role>|dict` is a dictionary describing **features** supported by the peer for that role.

With WAMP Basic Profile implementations, the *Features* dictionaries per *Role* will be empty. With WAMP Advanced Profile implementations, the *Features* dictionaries will list the (advanced) features supported per *Role*.

*Example: A Client that implements the Publisher and Subscriber roles of the WAMP Basic Profile.*

{align="left"}
        [1, "somerealm", {
          "roles": {
              "publisher": {},
              "subscriber": {}
          }
        }]

### WELCOME

A *Router* completes the opening of a WAMP session by sending a `WELCOME` reply message to the *Client*.

{align="left"}
        [WELCOME, Session|id, Details|dict]

where

* `Session` MUST be a randomly generated ID specific to the WAMP session. This applies for the lifetime of the session.
* `Details` is a dictionary that allows to provide additional information regarding the open session (see below).

In the WAMP Basic Profile without session authentication, a `WELCOME` message is the first message sent by the *Router*, directly in response to a `HELLO` message received from the *Client*.

> Note. The behavior if a requested `Realm` does not presently exist is router-specific. A router may e.g. automatically create the realm, or deny the establishment of the session with a `ABORT` reply message.
>

#### Router: Role and Feature Announcement

Similar to `HELLO` announcing *Roles* and *Features* supported by a *Client* to a *Router*, a *Router* announces its support to the *Client*  in an initial `WELCOME` message.

A *Router* must announce the **roles** it supports via `Welcome.Details.roles|dict`, with a key mapping to a `Welcome.Details.roles.<role>|dict` where `<role>` can be:

* `broker`
* `dealer`

A *Router* must support at least one role, and may support both roles.

The `<role>|dict` is a dictionary describing **features** supported by the peer for that role. With WAMP Basic Profile implementations, this will be empty.

*Example: A Router implementing the Broker role of the WAMP Basic Profile.*

{align="left"}
        [2, 9129137332, {
           "roles": {
              "broker": {}
           }
        }]

### ABORT

Both the *Router* and the *Client* may abort the opening of a WAMP session

-- adapt figure/hello_denied.png --

by sending an `ABORT` message.

{align="left"}
        [ABORT, Details|dict, Reason|uri]

where

* `Reason` MUST be an URI.
* `Details` is a dictionary that allows to provide additional, optional closing information (see below).

No response to an `ABORT` message is expected.

*Example*

{align="left"}
        [3, {"message": "The realm does not exist."}, 
            "wamp.error.no_such_realm"]


## Session Closing

A WAMP session starts its lifetime with the *Router* sending a `WELCOME` message to the *Client* and ends when the underlying transport disappears or when the WAMP session is closed explicitly by a `GOODBYE` message sent by one *Peer* and a `GOODBYE` message sent from the other *Peer* in response.

-- adapt figure/goodbye.png --

{align="left"}
        [GOODBYE, Details|dict, Reason|uri]

where 
 
* `Reason` MUST be an URI.
* `Details` is a dictionary that allows to provide additional, optional closing information (see below).

*Example*. One *Peer* initiates closing

{align="left"}
        [6, {"message": "The host is shutting down now."}, 
            "wamp.error.system_shutdown"]

and the other peer replies

{align="left"}
        [6, {}, "wamp.error.goodbye_and_out"]


*Example*. One *Peer* initiates closing

{align="left"}
        [6, {}, "wamp.error.close_realm"]

and the other peer replies

{align="left"}
        [6, {}, "wamp.error.goodbye_and_out"]


### Difference between ABORT and GOODBYE

The differences between `ABORT` and `GOODBYE` messages are:

1. `ABORT` gets sent only *before* a *Session* is established, while `GOODBYE` is sent only *after* a *Session* is already established.
2. `ABORT` is never replied by a *Peer*, whereas `GOODBYE` must be replied by the receiving *Peer*

> Though `ABORT` and `GOODBYE` are structurally identical, using different message types serves to reduce overloaded meaning of messages and simplify message handling code.
>


## Agent Identification

When a software agent operates in a network protocol, it often identifies itself, its application type, operating system, software vendor, or software revision, by submitting a characteristic identification string to its operating peer.

Similar to what browsers do with the `User-Agent` HTTP header, both the `HELLO` and the `WELCOME` message MAY disclose the WAMP implementation in use to its peer:

{align="left"}
        HELLO.Details.agent|string

and

{align="left"}
        WELCOME.Details.agent|string

*Example: A Client "HELLO" message.*

{align="left"}
        [1, "somerealm", {
             "agent": "AutobahnJS-0.9.14",
             "roles": {
                "subscriber": {},
                "publisher": {}
             }
        }]


*Example: A Router "WELCOME" message.*

{align="left"}
        [2, 9129137332, {
            "agent": "Crossbar.io-0.10.11",
            "roles": {
              "broker": {}
            }
        }]


# Publish and Subscribe

All of the following features for Publish & Subscribe are mandatory for WAMP Basic Profile implementations supporting the respective roles.


## Subscribing and Unsubscribing

The message flow between *Clients* implementing the role of *Subscriber* and *Routers* implementing the role of *Broker* for subscribing and unsubscribing involves the following messages:

1. `SUBSCRIBE`
2. `SUBSCRIBED`
3. `UNSUBSCRIBE`
4. `UNSUBSCRIBED`
5. `ERROR`

-- adapt figure/pubsub_subscribe.png --

A *Subscriber* may subscribe to zero, one or more topics, and a *Publisher* publishes to topics without knowledge of subscribers.

Upon subscribing to a topic via the `SUBSCRIBE` message, a *Subscriber* will receive any future events published to the respective topic by *Publishers*, and will receive those events asynchronously.

A subscription lasts for the duration of a session, unless a *Subscriber* opts out from a previously established subscription via the `UNSUBSCRIBE` message.

A *Subscriber* may have more than one event handler attached to the same subscription. This can be implemented in different ways:

* a *Subscriber* can recognize itself that it is already subscribed and just attach another handler to the subscription for incoming events
* or it can send a new `SUBSCRIBE` message to broker (as it would be first) and upon receiving a `SUBSCRIBED.Subscription|id` it already knows about, attach the handler to the existing subscription

### SUBSCRIBE

A *Subscriber* communicates its interest in a topic to a *Broker* by sending a `SUBSCRIBE` message:

{align="left"}
        [SUBSCRIBE, Request|id, Options|dict, Topic|uri]

where

 * `Request` is a random, ephemeral ID chosen by the *Subscriber* and used to correlate the *Broker's* response with the request.
 * `Options` is a dictionary that allows to provide additional subscription request details in a extensible way. This is described further below.
 * `Topic` is the topic the *Subscriber* wants to subscribe to.

*Example*

{align="left"}
        [32, 713845233, {}, "com.myapp.mytopic1"]

A *Broker*, receiving a `SUBSCRIBE` message, can fullfil or reject the subscription, so it answers with `SUBSCRIBED` or `ERROR` messages.

### SUBSCRIBED

If the *Broker* is able to fulfill and allow the subscription, it answers by sending a `SUBSCRIBED` message to the *Subscriber*

{align="left"}
        [SUBSCRIBED, SUBSCRIBE.Request|id, Subscription|id]

where

 * `SUBSCRIBE.Request` is the ID from the original request.
 * `Subscription` is an ID chosen by the *Broker* for the subscription.

*Example*

{align="left"}
        [33, 713845233, 5512315355]

> Note. The `Subscription` ID chosen by the broker need not be unique to the subscription of a single *Subscriber*, but may be assigned to the `Topic`, or the combination of the `Topic` and some or all `Options`, such as the topic pattern matching method to be used. Then this ID may be sent to all *Subscribers* for the `Topic` or `Topic` /  `Options` combination. This allows the *Broker* to serialize an event to be delivered only once for all actual receivers of the event.

> In case of receiving a `SUBSCRIBE` message from the same *Subscriber* and to already subscribed topic, *Broker* should answer with `SUBSCRIBED` message, containing the existing `Subscription|id`.

### Subscription ERROR

When the request for subscription cannot be fulfilled by the *Broker*, the *Broker* sends back an `ERROR` message to the *Subscriber*

{align="left"}
        [ERROR, SUBSCRIBE, SUBSCRIBE.Request|id, Details|dict, 
            Error|uri]

where

 * `SUBSCRIBE.Request` is the ID from the original request.
 * `Error` is an URI that gives the error of why the request could not be fulfilled.

*Example*

{align="left"}
        [8, 32, 713845233, {}, "wamp.error.not_authorized"]


### UNSUBSCRIBE

When a *Subscriber* is no longer interested in receiving events for a subscription it sends an `UNSUBSCRIBE` message

{align="left"}
        [UNSUBSCRIBE, Request|id, SUBSCRIBED.Subscription|id]

where

 * `Request` is a random, ephemeral ID chosen by the *Subscriber* and used to correlate the *Broker's* response with the request.
 * `SUBSCRIBED.Subscription` is the ID for the subscription to unsubscribe from, originally handed out by the *Broker* to the *Subscriber*.

*Example*

{align="left"}
        [34, 85346237, 5512315355]

### UNSUBSCRIBED

Upon successful unsubscription, the *Broker* sends an `UNSUBSCRIBED` message to the *Subscriber*

{align="left"}
        [UNSUBSCRIBED, UNSUBSCRIBE.Request|id]

where

 * `UNSUBSCRIBE.Request` is the ID from the original request.

*Example*

{align="left"}
        [35, 85346237]


### Unsubscribe ERROR

When the request fails, the *Broker* sends an `ERROR`

{align="left"}
        [ERROR, UNSUBSCRIBE, UNSUBSCRIBE.Request|id, Details|dict, 
            Error|uri]

where

 * `UNSUBSCRIBE.Request` is the ID from the original request.
 * `Error` is an URI that gives the error of why the request could not be fulfilled.

*Example*

{align="left"}
        [8, 34, 85346237, {}, "wamp.error.no_such_subscription"]



## Publishing and Events

The message flow between *Publishers*, a *Broker* and *Subscribers* for publishing to topics and dispatching events involves the following messages:

 1. `PUBLISH`
 2. `PUBLISHED`
 3. `EVENT`
 4. `ERROR`

-- adapt figure/pubsub_publish1.png --

### PUBLISH

When a *Publisher* requests to publish an event to some topic, it sends a `PUBLISH` message to a *Broker*:

{align="left"}
        [PUBLISH, Request|id, Options|dict, Topic|uri]

or

{align="left"}
        [PUBLISH, Request|id, Options|dict, Topic|uri, Arguments|list]

or

{align="left"}
        [PUBLISH, Request|id, Options|dict, Topic|uri, Arguments|list,
            ArgumentsKw|dict]

where

* `Request` is a random, ephemeral ID chosen by the *Publisher* and used to correlate the *Broker's* response with the request.
* `Options` is a dictionary that allows to provide additional publication request details in an extensible way. This is described further below.
* `Topic` is the topic published to.
* `Arguments` is a list of application-level event payload elements. The list may be of zero length.
* `ArgumentsKw` is an optional dictionary containing application-level event payload, provided as keyword arguments. The dictionary may be empty.

If the *Broker* is able to fulfill and allowing the publication, the *Broker* will send the event to all current *Subscribers* of the topic of the published event.

By default, publications are unacknowledged, and the *Broker* will not respond, whether the publication was successful indeed or not. This behavior can be changed with the option `PUBLISH.Options.acknowledge|bool` (see below).

*Example*

{align="left"}
        [16, 239714735, {}, "com.myapp.mytopic1"]

*Example*

{align="left"}
        [16, 239714735, {}, "com.myapp.mytopic1", ["Hello, world!"]]

*Example*

{align="left"}
        [16, 239714735, {}, "com.myapp.mytopic1", [], {"color": "orange",
            "sizes": [23, 42, 7]}]


### PUBLISHED

If the *Broker* is able to fulfill and allowing the publication, and `PUBLISH.Options.acknowledge == true`, the *Broker* replies by sending a `PUBLISHED` message to the *Publisher*:

{align="left"}
        [PUBLISHED, PUBLISH.Request|id, Publication|id]

where

* `PUBLISH.Request` is the ID from the original publication request.
* `Publication` is a ID chosen by the Broker for the publication.

*Example*

{align="left"}
        [17, 239714735, 4429313566]


### Publish ERROR

When the request for publication cannot be fulfilled by the *Broker*, and `PUBLISH.Options.acknowledge == true`, the *Broker* sends back an `ERROR` message to the *Publisher*

{align="left"}
        [ERROR, PUBLISH, PUBLISH.Request|id, Details|dict, Error|uri]

where

 * `PUBLISH.Request` is the ID from the original publication request.
 * `Error` is an URI that gives the error of why the request could not be fulfilled.

*Example*

{align="left"}
        [8, 16, 239714735, {}, "wamp.error.not_authorized"]


### EVENT

When a publication is successful and a *Broker* dispatches the event, it determines a list of receivers for the event based on *Subscribers* for the topic published to and, possibly, other information in the event.

Note that the *Publisher* of an event will never receive the published event even if the *Publisher* is also a *Subscriber* of the topic published to.

> WAMP Advanced Profile provides options for more detailed control over publication.
>

When a *Subscriber* is deemed to be a receiver, the *Broker* sends the *Subscriber* an `EVENT` message:

{align="left"}
        [EVENT, SUBSCRIBED.Subscription|id, PUBLISHED.Publication|id, 
            Details|dict]

or

{align="left"}
        [EVENT, SUBSCRIBED.Subscription|id, PUBLISHED.Publication|id, 
            Details|dict, PUBLISH.Arguments|list]

or

{align="left"}
        [EVENT, SUBSCRIBED.Subscription|id, PUBLISHED.Publication|id, 
        Details|dict, PUBLISH.Arguments|list, PUBLISH.ArgumentKw|dict]

where

* `SUBSCRIBED.Subscription` is the ID for the subscription under which the *Subscriber* receives the event - the ID for the subscription originally handed out by the *Broker* to the *Subscriber*.
* `PUBLISHED.Publication` is the ID of the publication of the published event.
* `Details` is a dictionary that allows the *Broker* to provide additional event details in a extensible way. This is described further below.
* `PUBLISH.Arguments` is the application-level event payload that was provided with the original publication request.
* `PUBLISH.ArgumentKw` is the application-level event payload that was provided with the original publication request.

*Example*

{align="left"}
        [36, 5512315355, 4429313566, {}]

*Example*

{align="left"}
        [36, 5512315355, 4429313566, {}, ["Hello, world!"]]

*Example*

{align="left"}
        [36, 5512315355, 4429313566, {}, [], {"color": "orange", 
            "sizes": [23, 42, 7]}]



# Remote Procedure Calls

All of the following features for Remote Procedure Calls are mandatory for WAMP Basic Profile implementations supporting the respective roles.


## Registering and Unregistering

The message flow between *Callees* and a *Dealer* for registering and unregistering endpoints to be called over RPC involves the following messages:

1. `REGISTER`
2. `REGISTERED`
4. `UNREGISTER`
5. `UNREGISTERED`
6. `ERROR`

-- adapt figure/rpc_register1.png --

### REGISTER

A *Callee* announces the availability of an endpoint implementing a procedure with a *Dealer* by sending a `REGISTER` message:

{align="left"}
        [REGISTER, Request|id, Options|dict, Procedure|uri]

where

* `Request` is a random, ephemeral ID chosen by the *Callee* and used to correlate the *Dealer's* response with the request.
* `Options` is a dictionary that allows to provide additional registration request details in a extensible way. This is described further below.
* `Procedure`is the procedure the *Callee* wants to register

*Example*

{align="left"}
        [64, 25349185, {}, "com.myapp.myprocedure1"]

### REGISTERED

If the *Dealer* is able to fulfill and allowing the registration, it answers by sending a `REGISTERED` message to the `Callee`:

{align="left"}
        [REGISTERED, REGISTER.Request|id, Registration|id]

where

* `REGISTER.Request` is the ID from the original request.
*  `Registration` is an ID chosen by the *Dealer* for the registration.

*Example*

{align="left"}
        [65, 25349185, 2103333224]

### Register ERROR

When the request for registration cannot be fulfilled by the *Dealer*, the *Dealer* sends back an `ERROR` message to the *Callee*:

{align="left"}
        [ERROR, REGISTER, REGISTER.Request|id, Details|dict, Error|uri]

where 

* `REGISTER.Request` is the ID from the original request.
* `Error` is an URI that gives the error of why the request could not be fulfilled.

*Example*

{align="left"}
        [8, 64, 25349185, {}, "wamp.error.procedure_already_exists"]

### UNREGISTER

When a *Callee* is no longer willing to provide an implementation of the registered procedure, it sends an `UNREGISTER` message to the *Dealer*:

{align="left"}
        [UNREGISTER, Request|id, REGISTERED.Registration|id]

where

* `Request` is a random, ephemeral ID chosen by the *Callee* and used to correlate the *Dealer's* response with the request.
* `REGISTERED.Registration` is the ID for the registration to revoke, originally handed out by the *Dealer* to the *Callee*.

*Example*

{align="left"}
        [66, 788923562, 2103333224]

### UNREGISTERED

Upon successful unregistration, the *Dealer* sends an `UNREGISTERED` message to the *Callee*:

{align="left"}
        [UNREGISTERED, UNREGISTER.Request|id]

where

* `UNREGISTER.Request` is the ID from the original request.

*Example*

{align="left"}
        [67, 788923562]

### Unregister ERROR

When the unregistration request fails, the *Dealer* sends an `ERROR` message:

{align="left"}
        [ERROR, UNREGISTER, UNREGISTER.Request|id, Details|dict, 
            Error|uri]

where

* `UNREGISTER.Request` is the ID from the original request.
* `Error` is an URI that gives the error of why the request could not be fulfilled.

*Example*

{align="left"}
        [8, 66, 788923562, {}, "wamp.error.no_such_registration"]


## Calling and Invocations

The message flow between *Callers*, a *Dealer* and *Callees* for calling procedures and invoking endpoints involves the following messages:

1. `CALL`
2. `RESULT`
3. `INVOCATION`
4. `YIELD`
5. `ERROR`

-- adapat figure/rpc_call1.png -- 

The execution of remote procedure calls is asynchronous, and there may be more than one call outstanding. A call is called outstanding (from the point of view of the *Caller*), when a (final) result or error has not yet been received by the *Caller*.

### CALL

When a *Caller* wishes to call a remote procedure, it sends a `CALL` message to a *Dealer*:

{align="left"}
        [CALL, Request|id, Options|dict, Procedure|uri]

or

{align="left"}
        [CALL, Request|id, Options|dict, Procedure|uri, Arguments|list]

or

{align="left"}
        [CALL, Request|id, Options|dict, Procedure|uri, Arguments|list, 
            ArgumentsKw|dict]

where

* `Request` is a random, ephemeral ID chosen by the *Caller* and used to correlate the *Dealer's* response with the request.
* `Options` is a dictionary that allows to provide additional call request details in an extensible way. This is described further below.
* `Procedure` is the URI of the procedure to be called.
* `Arguments` is a list of positional call arguments (each of arbitrary type). The list may be of zero length.
* `ArgumentsKw` is a dictionary of keyword call arguments (each of arbitrary type). The dictionary may be empty.

*Example*

{align="left"}
        [48, 7814135, {}, "com.myapp.ping"]

*Example*

{align="left"}
        [48, 7814135, {}, "com.myapp.echo", ["Hello, world!"]]

*Example*

{align="left"}
        [48, 7814135, {}, "com.myapp.add2", [23, 7]]

*Example*

{align="left"}
        [48, 7814135, {}, "com.myapp.user.new", ["johnny"], 
            {"firstname": "John", "surname": "Doe"}]


### INVOCATION

If the *Dealer* is able to fulfill (mediate) the call and it allows the call, it sends a `INVOCATION` message to the respective *Callee* implementing the procedure:

{align="left"}
        [INVOCATION, Request|id, REGISTERED.Registration|id, 
            Details|dict]

or

{align="left"}
        [INVOCATION, Request|id, REGISTERED.Registration|id, 
            Details|dict, CALL.Arguments|list]

or

{align="left"}
        [INVOCATION, Request|id, REGISTERED.Registration|id, 
            Details|dict, CALL.Arguments|list, CALL.ArgumentsKw|dict]

where

* `Request` is a random, ephemeral ID chosen by the *Dealer* and used to correlate the *Callee's* response with the request.
* `REGISTERED.Registration` is the registration ID under which the procedure was registered at the *Dealer*.
* `Details` is a dictionary that allows to provide additional invocation request details in an extensible way. This is described further below.
* `CALL.Arguments` is the original list of positional call arguments as provided by the *Caller*.
* `CALL.ArgumentsKw` is the original dictionary of keyword call arguments as provided by the *Caller*.

*Example*

{align="left"}
        [68, 6131533, 9823526, {}]

*Example*

{align="left"}
        [68, 6131533, 9823527, {}, ["Hello, world!"]]

*Example*

{align="left"}
        [68, 6131533, 9823528, {}, [23, 7]]

*Example*

{align="left"}
        [68, 6131533, 9823529, {}, ["johnny"], {"firstname": "John", 
            "surname": "Doe"}]


### YIELD

If the *Callee* is able to successfully process and finish the execution of the call, it answers by sending a `YIELD` message to the *Dealer*:

{align="left"}
        [YIELD, INVOCATION.Request|id, Options|dict]

or

{align="left"}
        [YIELD, INVOCATION.Request|id, Options|dict, Arguments|list]

or

{align="left"}
        [YIELD, INVOCATION.Request|id, Options|dict, Arguments|list, 
            ArgumentsKw|dict]

where

* `INVOCATION.Request` is the ID from the original invocation request.
* `Options`is a dictionary that allows to provide additional options.
* `Arguments` is a list of positional result elements (each of arbitrary type). The list may be of zero length.
* `ArgumentsKw` is a dictionary of keyword result elements (each of arbitrary type). The dictionary may be empty.


*Example*

{align="left"}
        [70, 6131533, {}]

*Example*

{align="left"}
        [70, 6131533, {}, ["Hello, world!"]]

*Example*

{align="left"}
        [70, 6131533, {}, [30]]

*Example*

{align="left"}
        [70, 6131533, {}, [], {"userid": 123, "karma": 10}]


### RESULT

The *Dealer* will then send a `RESULT` message to the original *Caller*:

{align="left"}
        [RESULT, CALL.Request|id, Details|dict]

or

{align="left"}
        [RESULT, CALL.Request|id, Details|dict, YIELD.Arguments|list]

or

{align="left"}
        [RESULT, CALL.Request|id, Details|dict, YIELD.Arguments|list, 
            YIELD.ArgumentsKw|dict]

where

* `CALL.Request` is the ID from the original call request.
* `Details` is a dictionary of additional details.
* `YIELD.Arguments` is the original list of positional result elements as returned by the *Callee*.
* `YIELD.ArgumentsKw` is the original dictionary of keyword result elements as returned by the *Callee*.

*Example*

{align="left"}
        [50, 7814135, {}]

*Example*

{align="left"}
        [50, 7814135, {}, ["Hello, world!"]]

*Example*

{align="left"}
        [50, 7814135, {}, [30]]

*Example*

{align="left"}
        [50, 7814135, {}, [], {"userid": 123, "karma": 10}]


### Invocation ERROR


If the *Callee* is unable to process or finish the execution of the call, or the application code implementing the procedure raises an exception or otherwise runs into an error, the *Callee* sends an `ERROR` message to the *Dealer*:

{align="left"}
        [ERROR, INVOCATION, INVOCATION.Request|id, Details|dict, 
            Error|uri]

or

{align="left"}
        [ERROR, INVOCATION, INVOCATION.Request|id, Details|dict, 
        Error|uri, Arguments|list]

or

{align="left"}
        [ERROR, INVOCATION, INVOCATION.Request|id, Details|dict, 
            Error|uri, Arguments|list, ArgumentsKw|dict]

where

* `INVOCATION.Request` is the ID from the original `INVOCATION` request previously sent by the *Dealer* to the *Callee*.
* `Details` is a dictionary with additional error details.
* `Error` is an URI that identifies the error of why the request could not be fulfilled.
* `Arguments` is a list containing arbitrary, application defined, positional error information. This will be forwarded by the *Dealer* to the *Caller* that initiated the call.
* `ArgumentsKw` is a dictionary containing arbitrary, application defined, keyword-based error information. This will be forwarded by the *Dealer* to the *Caller* that initiated the call.

*Example*

{align="left"}
        [8, 68, 6131533, {}, "com.myapp.error.object_write_protected",
            ["Object is write protected."], {"severity": 3}]


### Call ERROR

The *Dealer* will then send a `ERROR` message to the original *Caller*:

{align="left"}
        [ERROR, CALL, CALL.Request|id, Details|dict, Error|uri]

or

{align="left"}
        [ERROR, CALL, CALL.Request|id, Details|dict, Error|uri, 
            Arguments|list]

or

{align="left"}
        [ERROR, CALL, CALL.Request|id, Details|dict, Error|uri, 
            Arguments|list, ArgumentsKw|dict]

where

* `CALL.Request` is the ID from the original `CALL` request sent by the *Caller* to the *Dealer*.
* `Details` is a dictionary with additional error details.
* `Error` is an URI identifying the type of error as returned by the *Callee* to the *Dealer*.
* `Arguments` is a list containing the original error payload list as returned by the *Callee* to the *Dealer*.
* `ArgumentsKw` is a dictionary containing the original error payload dictionary as returned by the *Callee* to the *Dealer*

*Example*

{align="left"}
        [8, 48, 7814135, {}, "com.myapp.error.object_write_protected",
            ["Object is write protected."], {"severity": 3}]

If the original call already failed at the *Dealer* **before** the call would have been forwarded to any *Callee*, the *Dealer* will send an `ERROR` message to the *Caller*:

{align="left"}
        [ERROR, CALL, CALL.Request|id, Details|dict, Error|uri]

*Example*

{align="left"}
        [8, 48, 7814135, {}, "wamp.error.no_such_procedure"]










# Security Considerations

-- write me --

# IANA Considerations

TBD

# Appendixes

# References

# Contributors

# Acknowledgements

<reference anchor='DSM-IV' target='http://www.psychiatryonline.com/resourceTOC.aspx?resourceID=1'>
  <front>
   <title>Diagnostic and Statistical Manual of Mental Disorders (DSM)</title>
   <author></author>
   <date></date>
  </front>
</reference>

{backmatter}