# Cadmium Protocol core specifications

## 0. Table of content

- [Cadmium Protocol core specifications](#cadmium-protocol-core-specifications)
  - [0. Table of content](#0-table-of-content)
  - [1. Transport](#1-transport)
  - [2. Federated Entity ID](#2-federated-entity-id)
    - [2.1. Server-part](#21-server-part)
    - [2.2. Username/Room alias/RoomID](#22-usernameroom-aliasroomid)
    - [2.3. Special business rules](#23-special-business-rules)
  - [3. BaseMessage model](#3-basemessage-model)
  - [4. Protocol Errors](#4-protocol-errors)
    - [4.1. Use cases](#41-use-cases)
    - [4.2. JSON Schema](#42-json-schema)
      - [4.2.1. Payload](#421-payload)
  - [5. Basic request ratelimit system](#5-basic-request-ratelimit-system)
    - [5.1. Use cases](#51-use-cases)
    - [5.2. Error types](#52-error-types)
      - [5.2.1. Global error types](#521-global-error-types)
    - [5.3. Business Rules](#53-business-rules)
    - [5.4. JSON Schema](#54-json-schema)
      - [5.4.1. Error payload](#541-error-payload)
  - [6. User authorization](#6-user-authorization)
    - [6.1. Message Type Identifier](#61-message-type-identifier)
    - [6.2. Authorization types](#62-authorization-types)
    - [6.3. Common Flow](#63-common-flow)
    - [6.4. JSON Schema](#64-json-schema)

## 1. Transport

We are using Websockets for underlying connections and JSON for message serialization format.

## 2. Federated Entity ID

Some reserved formats:

- Room alias: `#<roomAlias>@<serverpart>`
- Username: `@<username>@<serverpart>`
- User ID with any 3PID: `%<type>:<data>@<serverpart>`
  - Currently supported only following types: `email` and `msisdn`.
- Raw User ID: `@<UUID>@<serverpart>`
- Message ID: `&msg:<uuid>@<serverpart of source server>`
- Media ID: `&media:<uuid>@<content service serverpart>`
- Group Chat ID: `!<roomID>@<serverpart>`
- Single server-part: `<serverpart>`

### 2.1. Server-part

- hostname: `IPv4 / [IPv6] / dns-domain:<port (1-65535)>` (for end-users use)
- server ID: static SHA256 hash string from 4096 characters (for internal protocol use).
  - Generation example (using Linux): `echo -n $(cat /dev/urandom | tr -dc 'a-zA-Z0-9' | fold -w 4096 | head -n 1) | sha256sum`

### 2.2. Username/Room alias/RoomID

MUST NOT be empty, and MUST contain only the characters `a-z`, `0-9`, `.`, `_`, `=`, `-`, and `/`.

### 2.3. Special business rules

- Room ID MUST be UUID identifier.
- Servers MUST use server ID in internal purposes instead of normal server-part with hostname. Only end-users MUST use normal server-part with hostname. This is done for easy multi-domain serving.

## 3. BaseMessage model

BaseMessage is a basic message model, basis of the whole protocol. It is used for a very easy protocol extension process.

BaseMessage scheme:

```typescript
interface BaseMessage {
    /**
     * Message identifier (used to track the request-response chain)
     */
    id: string,

    /**
     * Type of message (used to determine which extension this message belongs to)
     */
    type: string,

    /**
     * From which entity this message is send
     */
    from: EntityID,

    /**
     * Message recipients
     */
    to: EntityID[],

    /**
     * Operation success indicator (used to determine if the error happened while processing request) - MUST be only in response from server
     */
    ok: boolean,

    /**
     * Message payload (used to store extra information in message, list of permissible fields in the payload depends on "type" field)
     */
    payload: Map<K,V>
}
```

## 4. Protocol Errors

Mechanism of error processing is included into protocol.  
Adds into any response message `ok` variable. If `ok` is true - we have no errors, if `ok` is false - we have an error.

### 4.1. Use cases

Client:

```json
{
    "id": "abcd",
    "type": "incorrectMessageType",
    "from": "@juliet@cadmium.im",
    "to": "cadmium.im",
    "payload": {
        "test": "test"
    }
}
```

Server:

```json
{
    "id": "abcd",
    "type": "incorrectMessageType",
    "from": "cadmium.im",
    "to": "@juliet@cadmium.im",
    "ok": false,
    "payload": {
        "errID": "unhandled",
        "errText": "Incorrect type of message (type isn't implemented in the server)",
        "errPayload": {}
    }
}
```

### 4.2. JSON Schema

#### 4.2.1. Payload

```typescript
interface ErrorPayload {
    /**
     * Error identifier (defined in extensions, maybe same per extensions)
     */
    errID: string,

    /**
     * Explanation of error in human-readable view
     */
    errText: string,

    /**
     * Advanced error information (fields defined in extensions)
     */
    errPayload: Map<K,V>
}
```


## 5. Basic request ratelimit system

This system is intended to limit the number of requests from clients per unit of time.

### 5.1. Use cases

- Client:

```json
{
    "id": "abcd",
    "type": "profile:register",
    "to": "cadmium.org",
    "payload": {
        "username": "spam_spam_spam",
        "thirdPIDs": [],
        "password": "spam"
    }
}
```

- Server:

```json
{
    "id": "abcd",
    "type": "profile:register",
    "from": "cadmium.org",
    "ok": false,
    "payload": {
        "errID": "ratelimit_exceed",
        "errText": "Request ratelimit exceed! Please, try again later!",
        "errPayload": {
            "retryAfter": 1000
        }
    }
}
```

### 5.2. Error types

#### 5.2.1. Global error types

- `ratelimit_exceed`

### 5.3. Business Rules

- Server MUST count number of requests per unit of time and drop new requests after specified number of made requests with Protocol Error message.
- Number of requests and used unit of time SHOULD be configurable on server

### 5.4. JSON Schema

#### 5.4.1. Error payload

```typescript
interface RatelimitExceedErrorPayload {
    /**
     * How long after the client can retry the request (in millis)
     */
    retryAfter: number
}
```

## 6. User authorization

Authorization has multiple types which described in this document. Auth flow can vary depending on selected type of authentication. Common flow of auth is described in this document. When authorization was passed successfully, server must mark current session as authorized.

### 6.1. Message Type Identifier

`urn:cadmium:auth`

### 6.2. Authorization types

- `urn:cadmium:auth:login_password` - simple login/pass authorization
- `urn:cadmium:auth:token` - authorization by token

### 6.3. Common Flow

Flow for user authorization (in this example type of auth is `login_password` and `token`):

1. User obtains correct user/pass pair.
2. User sends this pair to server:

C->S:
```json
{
    "id": "abcd",
    "type": "urn:cadmium:auth",
    "to": ["cadmium.org"],
    "payload": {
		"type": "urn:cadmium:auth:login_password",
		"fields": {
			"username": "juliet",
			"password": "romeo1"
		}
    }
}
```

S->C:
```json
{
    "id": "abcd",
    "type": "urn:cadmium:auth",
    "from": "cadmium.org",
    "ok": true,
    "payload": {
        "token": "3b5135a5-aff5-4396-a629-a254f383e82f",
        "deviceID": "ABCDEFG"
    }
}
```

3. Afterwards, user uses obtained auth token for every new session to authorize it:

C->S:
```json
{
    "id": "abcd",
    "type": "urn:cadmium:auth",
    "to": ["cadmium.org"],
    "payload": {
		"type": "urn:cadmium:auth:token",
		"fields": {
			"token": "3b5135a5-aff5-4396-a629-a254f383e82f",
		}
    }
}
```

S->C:

```json
{
    "id": "abcd",
    "type": "urn:cadmium:auth",
    "from": "cadmium.org",
    "ok": true
}
```

### 6.4. JSON Schema

Request:
```typescript
interface AuthReq {
	type: string; // type of authorization
	fields: AuthFields; // auth fields (depends on type of auth)
}

interface AuthFields {}

// urn:cadmium:auth:login_password
interface LoginPassFields : AuthFields {
	username: string;
	password: string;
}

// urn:cadmium:auth:token
interface TokenFields : AuthFields {
	token: string;
}
```

Response:
```typescript
interface AuthResp {
	token: string; // newly created authorization token
	deviceID: string; // ID of newly created device (session id in other words)
}
```