# Base

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