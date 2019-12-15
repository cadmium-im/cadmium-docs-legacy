# Protocol Core

- [Protocol Core](#protocol-core)
  - [Transport](#transport)
  - [Entity ID](#entity-id)
  - [BaseMessage](#basemessage)

## Transport
For starting we simply use JSON + Websockets.

## Entity ID
* Room alias: `#<roomAlias>@<serverpart>`
* Username: `@<username>@<serverpart>`
* User ID with any 3PID: `%<type>:<data>@<serverpart>`
  * Currently supported only following types: `email` and `msisdn`.
* Raw User ID: `@<UUID>@<serverpart>`
* Message ID: `&<uuid>@<serverpart (from which server the message was sent)>`
* Room ID: `!<roomID>@<serverpart>`
* Single server-part: `<serverpart>`

**Server-part**:
- hostname: `IPv4 / [IPv6] / dns-domain:<port (1-65535)>` (for end-users use)
- server ID: static SHA256 hash string from 4096 characters (for internal protocol use) 

**Username/Room alias/RoomID** - MUST NOT be empty, and MUST contain only the characters `a-z`, `0-9`, `.`, `_`, `=`, `-`, and `/`.

**Special business rules**: 
- RoomID SHOULD be UUID identifier.
- Servers MUST use server ID in internal purposes instead of normal server-part with hostname. Only end-users MUST use normal server-part with hostname. This is done for easy multi-domain serving.

## BaseMessage
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
     * Message recipient
     */
    to: EntityID,

    /**
     * Operation success indicator (used to determine if the error happened while processing request)
     */
    ok: boolean,

    /**
     * Message payload (used to store extra information in message, list of permissible fields in the payload depends on "type" field)
     */
    payload: Map<K,V>
}
```