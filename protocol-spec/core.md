# Protocol Core

## Entity ID
* Room alias: `#<roomAlias>:<serverpart>`
* Username: `@<username>@<serverpart>`
* User ID with MSISDN (Mobile Station International Subscriber Directory Number): `%<msisdn without +>:<serverpart>`
* User ID with Email: `^<email username>_at_<email hostname>:<serverpart>`
* Message ID: `&<uuid>:<serverpart (from which server the message was sent)>`
* Room ID: `!<roomID>@<serverpart>`
* Single server-part: `<serverpart>`

**Server-part (hostname)** - `IPv4 / [IPv6] / dns-domain:<port (1-65535)>`.  
**Username/Room alias/RoomID** - MUST NOT be empty, and MUST contain only the characters `a-z`, `0-9`, `.`, `_`, `=`, `-`, and `/`.

RoomID SHOULD be UUID identifier.

## BaseMessage
BaseMessage is a basic message model, basis of the whole protocol. It is used for a very easy protocol extension process.

BaseMessage scheme:
```
id (string) - identifier of message (used to track the request-response chain)
type (string) - type of message (used to determine which extension this message belongs to)
from (EntityID) - from which entity this message is send
to (EntityID) - message recipient
ok (boolean) - operation success indicator (used to determine if errors was happened when processing request)
payload (Map<K,V>) - message payload (used to store extra information in message, list of permissible fields in the payload are depends on "type" field)
```