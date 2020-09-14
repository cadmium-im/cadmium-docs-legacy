# Basic request ratelimit system

## Introduction

This core extension is intended to limit the number of requests from clients per unit of time.

## Message type identifiers

None.

## Use cases

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

## Errors

### Global errors

- `ratelimit_exceed`

## Business Rules

- Server MUST count number of requests per unit of time and drop new requests after specified number of made requests with Protocol Error message.
- Number of requests and used unit of time SHOULD be configurable on server

## JSON Schema

### Error payload

```typescript
interface RatelimitExceedErrorPayload {
    /**
     * How long after the client can retry the request (in millis)
     */
    retryAfter: number
}
```
