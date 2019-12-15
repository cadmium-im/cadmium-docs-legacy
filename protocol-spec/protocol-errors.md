# Protocol Errors
## Introduction
Mechanism of error processing included into protocol.  
Adds into any type ID `:error` postfix.

## Message type identifiers
- `*:error`

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

## JSON Schema
**Payload**

```typescript
interface ErrorPayload {
    /**
     * Error code (defined in extensions, may be same per extensions)
     */
    errCode: number,

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