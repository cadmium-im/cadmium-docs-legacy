# CPE#3 - In-band account registration

## Introduction

This extension is intended for creating user accounts on a server  

## Message type identifiers

- `profile:register`

## Errors

- Ratelimit system: enabled
- `id_exists`: username/third party ID already taken
- `reg_disabled`: registration isn't allowed on a server

## Use cases

### Basic registration flow

`// TODO: introduce email/msisdn confirmation which prevents spam attacks`

- Client:

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
        "password": "romeo1",
        "loginOnSuccess": false
    }
}
```

- Server:

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

- Error response:

```json
{
    "id": "abcd",
    "type": "profile:register",
    "from": "cadmium.org",
    "ok": false,
    "payload": {
        "errID": "id_exists",
        "errText": "Username/email/msisdn already taken"
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
    username?: string,

    /**
     * Array of user third party IDs (email and/or MSISDN)
     */
    thirdPIDs: ThirdPartyID[],

    /**
     * Password of new account
     */
    password: string,

    /**
     * Login to freshly created user account when registration will be completed
     */
    loginOnSuccess: boolean
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
    userID: EntityID,

    /**
     * Property with login payload (can be omit if property loginOnSuccess wasn't indicated true in RegistrationRequestPayload)
     */
    loginPayload?: LoginResponsePayload
}
```
