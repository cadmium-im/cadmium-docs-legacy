# Account registration

## Introduction

This extension is intended for creating user accounts on a server  

## Message type identifiers

- `profile:register`

## Error codes

- 0: limit exceed
- 1: username/third party ID already taken
- 2: registration isn't allowed on a server

## Use cases

- Request:

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

- Response:

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

## Business Rules

None.

## JSON Schema

### Payload

- Request:

```typescript
interface RegistrationRequestPayload {
    /**
     * The username that the user wants to register
     */
    username: string,

    /**
     * Array of user third party IDs (email and/or MSISDN)
     */
    thirdPIDs: ThirdPartyID[],

    /**
     * Password of new account
     */
    password: string
}

interface ThirdPartyID {
    /**
     * Type of third party ID
     */
    type: string,

    /**
     * String contains third party ID. Examples: "juliet@capulett.com", "+1234567890".
     */
    value: string
}
```

- Response:

```typescript
interface RegistrationResponsePayload {
    /**
     * ID of user (Username in priority. If we haven't username, then we put to this field one of user's third party IDs)
     */
    userID: EntityID
}
```
