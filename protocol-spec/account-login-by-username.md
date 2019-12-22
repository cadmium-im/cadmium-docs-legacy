# Account login by username

## Introduction

This extension is intended for logging into user account on a server by username

## Message type identifiers

- `profile:login`  

## Error codes

- 0: limit exceed
- 1: user ID/password isn't valid

## Use cases

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

## Business Rules

None.

## JSON Schema

### Payload

- Request:

```typescript
interface LoginRequestPayload {
    /**
     * The username of account which user wants to login
     */
    username: string,


    /**
     * Password of new account
     */
    password: string
}
```

- Response:

```typescript
interface LoginResponsePayload {
    /**
     * Authentication token which required for various user actions (UUID)
     */
    authToken: string,


    /**
     * Identifier of new user device (created by this login action)
     */
    deviceID: string
}
```