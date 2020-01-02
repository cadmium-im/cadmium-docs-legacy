# Protocol Errors

## Introduction

Mechanism of error processing included into protocol.  
Adds into any response message `ok` variable. If `ok` is true - we have no errors, if `ok` is false - we have an error.

## Message type identifiers

None.

## Use cases

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
    "type": "incorrectMessageType",
    "from": "cadmium.im",
    "to": "@juliet@cadmium.im",
    "ok": false,
    "payload": {
        "errCode": 0,
        "errText": "Incorrect type of message (type isn't implemented in the server)",
        "errPayload": {}
    }
}
```

## JSON Schema

### Payload

```typescript
interface ErrorPayload {
    /**
     * Error identifier (defined in extensions, maybe same per extensions)
     */
    errId: string,

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
