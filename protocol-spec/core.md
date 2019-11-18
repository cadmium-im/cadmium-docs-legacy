# Protocol Core

- [Protocol Core](#protocol-core)
  - [Transport](#transport)
  - [Entity ID](#entity-id)
  - [BaseMessage](#basemessage)
  - [Errors](#errors)
  - [User devices](#user-devices)
  - [Account registration/login](#account-registrationlogin)
    - [Create account](#create-account)
    - [Login into account (by username)](#login-into-account-by-username)

## Transport
For starting we simply use JSON + Websockets.

## Entity ID
* Room alias: `#<roomAlias>@<serverpart>`
* Username: `@<username>@<serverpart>`
* User ID with MSISDN (Mobile Station International Subscriber Directory Number): `%<msisdn without +>@<serverpart>`
* User ID with Email: `^<email username>_at_<email hostname>@serverpart>`
* Message ID: `&<uuid>@<serverpart (from which server the message was sent)>`
* Room ID: `!<roomID>@<serverpart>`
* Single server-part: `<serverpart>`

**Server-part (hostname)** - `IPv4 / [IPv6] / dns-domain:<port (1-65535)>`.  
**Username/Room alias/RoomID** - MUST NOT be empty, and MUST contain only the characters `a-z`, `0-9`, `.`, `_`, `=`, `-`, and `/`.

RoomID SHOULD be UUID identifier.

## BaseMessage
BaseMessage is a basic message model, basis of the whole protocol. It is used for a very easy protocol extension process.

BaseMessage scheme:
```
id (string) - message identifier (used to track the request-response chain)
type (string) - type of message (used to determine which extension this message belongs to)
from (EntityID) - from which entity this message is send
to (EntityID) - message recipient
ok (boolean) - operation success indicator (used to determine if the error happened while processing request)
payload (Map<K,V>) - message payload (used to store extra information in message, list of permissible fields in the payload depends on "type" field)
```

## Errors
Mechanism of error processing included into protocol.
Adds into type name `:error` postfix.

**Payload**:
* `errCode: int` - error code (defined in extensions, may be same per extensions)
* `errText: String` - explanation of error in human-readable view
* `errPayload: Map<K,V>` - advanced error information (fields defined in extensions)

**Usecase**:  

*Request*:
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

*Response*:
```json
{
    "id": "abcd",
    "type": "incorrectMessageType:error",
    "from": "cadmium.im",
    "to": "@juliet@cadmium.im",
    "payload": {
        "errCode": 0,
        "errText": "Incorrect type of message (type isn't implemented in the server)",
        "errPayload": {}
    }
}
```

## User devices
`todo`

## Account registration/login
### Create account
**Description**: Create user account on a server  
**Type**: `profile:register`  
**Payload**:
- Request: 
  - `username: string` - the username that the user wants to register
  - `thirdPIDs: []` - array of user third party IDs (email and/or MSISDN). Array contains objects with following fields: 
    - `type: string` - type of third party ID.
    - `value: string` - string contains third party ID. Examples: `juliet@capulett.com`, `+1234567890`.
  - `password: string` - password of new account
- Response:
  - `userID: EntityID`- ID of user (Username in priority. If we haven't username, then we put to this field one of user's third party IDs).  

**Errors**:
- 0: limit exceed
- 1: username/third party ID already taken
- 2: registration isn't allowed on a server

**Usecase**:

*Request*:
```json
{
    "id": "abcd",
    "type": "profile:register",
    "to": "cadmium.org",
    "payload": {
        "username": "juliet",
        "thirdPIDs": [
            {"type":"email", "value":"juliet@capulett.com"},
            {"type":"msisdn", "value":"+1234567890"},
        ],
        "password": "romeo1"
    }
}
```

*Response*:
```json
{
    "id": "abcd",
    "type": "profile:register",
    "from": "cadmium.org",
    "ok": true,
    "payload": {
        "userID": "@juliet@cadmium.org"
    }
}
```

*<b>Error</b> response*:
```json
{
    "id": "abcd",
    "type": "profile:register",
    "from": "cadmium.org",
    "ok": false,
    "payload": {
        "errCode": 1,
        "errText": "{Username/email/msisdn} already taken"
    }
}
```

**Special business rules**: none.

### Login into account (by username)
**Description**: Login into user account on a server by username
**Type**: `profile:login`  
**Payload**:
- Request: 
  - `username: string` - the username of account which user wants to login
  - `password: string` - password of new account
- Response:
  - `authToken: string` - authentication token which required for various user actions (UUID)
  - `deviceID: string` - identifier of new user device (created by this login action)

**Errors**:  
- 0: limit exceed
- 1: user ID/password isn't valid

**Usecase**:

*Request*:
```json
{
    "id": "abcd",
    "type": "profile:login",
    "to": "cadmium.org",
    "payload": {
        "username": "juliet",
        "password": "romeo1"
    }
}
```

*Response*:
```json
{
    "id": "abcd",
    "type": "profile:login",
    "from": "cadmium.org",
    "ok": true,
    "payload": {
        "authToken": "3b5135a5-aff5-4396-a629-a254f383e82f",
        "deviceID": "ABCDEFG"
    }
}
```

*<b>Error</b> response*:
```json
{
    "id": "abcd",
    "type": "profile:login",
    "from": "cadmium.org",
    "ok": false,
    "payload": {
        "errCode": 1,
        "errText": "Username/password isn't valid"
    }
}
```

**Special business rules**: none.

