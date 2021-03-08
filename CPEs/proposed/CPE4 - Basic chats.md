# CPE#4 - Basic chats

## 0. Table of content

- [CPE#4 - Basic chats](#cpe4---basic-chats)
  - [0. Table of content](#0-table-of-content)
  - [1. Introduction](#1-introduction)
  - [2. Requirements](#2-requirements)
  - [3. Message type identifiers](#3-message-type-identifiers)
  - [4. Errors](#4-errors)
    - [4.1. Error types](#41-error-types)
  - [5. Event types](#5-event-types)
    - [5.1. Media types](#51-media-types)
  - [6. Use cases](#6-use-cases)
    - [6.1. Exchanging messages](#61-exchanging-messages)
    - [6.2. Exchanging media](#62-exchanging-media)
    - [6.3. Read messages](#63-read-messages)
    - [6.4. Typing message](#64-typing-message)
    - [6.5. Message history API](#65-message-history-api)
      - [6.5.1. History retrieving](#651-history-retrieving)
  - [7. Business Rules](#7-business-rules)
    - [7.1. Typing message notification](#71-typing-message-notification)
    - [7.2. Message/Media ID](#72-messagemedia-id)
  - [8. JSON Schema](#8-json-schema)
    - [Event Entity](#event-entity)
      - [Content sections](#content-sections)
    - [Send message (`urn:cadmium:chat:message`)](#send-message-urncadmiumchatmessage)
      - [Request](#request)
      - [Response](#response)
    - [Read message notification (`urn:cadmium:chat:read`)](#read-message-notification-urncadmiumchatread)
      - [Request](#request-1)
    - [Edit message notification (`urn:cadmium:chat:message:edit`)](#edit-message-notification-urncadmiumchatmessageedit)
      - [Request](#request-2)
    - [Delete message notification (`urn:cadmium:chat:message:delete`)](#delete-message-notification-urncadmiumchatmessagedelete)
      - [Request](#request-3)
    - [Sync history of user chats (`urn:cadmium:chat:history:sync`)](#sync-history-of-user-chats-urncadmiumchathistorysync)
      - [Request](#request-4)
      - [Response](#response-1)

## 1. Introduction

This extension is intended for organizing chats between some entities.

## 2. Requirements

- Exchanging text messages
- Typing status
- Message reading
- Sharing files/media
- Forwards and replies to messages
- Stickers/GIF
- Message reactions

## 3. Message type identifiers

- `urn:cadmium:chat:message` - regular message
- `urn:cadmium:chat:read` - read message notification
- `urn:cadmium:chat:typing` - typing message notification
- `urn:cadmium:chat:message:edit` - edit message notification
- `urn:cadmium:chat:message:delete` - delete message notification
- `urn:cadmium:chat:history:sync` - sync history of user chats

## 4. Errors

- Ratelimit system: disabled
- Authorization: enabled

### 4.1. Error types

- `urn:cadmium:chat:private:banned` - Sending messages to user is prohibited, because he banned the sender of current message - in case if we send the message to user entity.

## 5. Event types

- `urn:cadmium:chat:event:message` - message with text and optional media

### 5.1. Media types

- `urn:cadmium:chat:media:photo` - a photo
- `urn:cadmium:chat:media:audio` - an audio or voice message
- `urn:cadmium:chat:media:video` - a video or rounded video message
- `urn:cadmium:chat:media:file` - any other file type
- `urn:cadmium:chat:media:sticker` - a sticker
- `urn:cadmium:chat:media:gif` - an animated GIF 

## 6. Use cases

### 6.1. Exchanging messages

1. C1->S - Sending message:

    ```json
    {
        "id": "abcd",
        "type": "urn:cadmium:chat:message",
        "to": ["@user2@cadmium.org"],
        "payload": {
            "type": "urn:cadmium:chat:event:message",
            "content": {
                "text": "hello world",
            }
        }
    }
    ```

    S->C1 - Successful response:

    ```json
    {
        "id": "abcd",
        "type": "urn:cadmium:chat:message",
        "from": "cadmium.org",
        "ok": true,
        "payload": {
            "eventID": "3b5135a5-aff5-4396-a629-a254f383e82f",
            "originServerTimestamp": 1599327584
        }
    }
    ```

    S->C1 - **Error** response:

    ```json
    {
        "id": "abcd",
        "type": "urn:cadmium:chat:message",
        "from": "cadmium.org",
        "ok": false,
        "payload": {
            "errID": "urn:cadmium:chat:private:banned",
            "errText": "The user has banned you"
        }
    }
    ```

2. S->C2:

    ```json
    {
        "id": "abcd",
        "type": "urn:cadmium:chat:message",
        "from": "@user1@cadmium.im",
        "ok": true,
        "payload": {
            "eventID": "3b5135a5-aff5-4396-a629-a254f383e82f",
            "originServerTimestamp": 1599327584,
            "type": "urn:cadmium:chat:event:message",
            "content": {
                "text": "hello world",
            }
        }
    }
    ```

### 6.2. Exchanging media

1. Upload the media to Content Service (explanation out of scope of this document)
2. Send message containing the metadata of uploaded media:

C1->S:
   ```json
    {
        "id": "abcd",
        "type": "urn:cadmium:chat:message",
        "to": ["@user2@cadmium.im"],
        "payload": {
            "type": "urn:cadmium:chat:event:message",
            "content": {
                "text": "",
                "media": [
                    {
                        "id": "4a771ac1-f0b2-4a4a-9700-f2a26fa2bb67@content.cadmium.im"
                    }
                ]
            }
            
        }
    }
   ```

S->C1:
   ```json
   {
        "id": "abcd",
        "type": "urn:cadmium:chat:message",
        "from": "cadmium.im",
        "ok": true,
        "payload": {
            "messageID": "7a1b9a72-1677-4476-9b22-ef7fdbcff52e",
            "originServerTimestamp": 1599329232
        }
    }
   ```

   **Note**: The Cadmium server will validate media ID which was provided by client. If it doesn't exist or invalid, server will respond with corresponding error.

3. Receive message with media on other side:

S->C2:

   ```json
   {
        "id": "defg",
        "type": "urn:cadmium:chat:message",
        "from": "@user1@cadmium.im",
        "ok": true,
        "payload": {
            "messageID": "7a1b9a72-1677-4476-9b22-ef7fdbcff52e",
            "originServerTimestamp": 1599329232,
            "type": "urn:cadmium:chat:event:message",
            "content": {
                "text": "",
                "media": [
                    {
                        "id": "4a771ac1-f0b2-4a4a-9700-f2a26fa2bb67@content.cadmium.im",
                        "type": "urn:cadmium:chat:media:photo",
                        "attrs": {
                            "width": 512,
                            "height": 512 
                        },
                        "size": 96413, // 96kb
                    }
                ]
            }
        }
    }
   ```

### 6.3. Read messages

1. Read message

    ```json
    {
        "id": "abcd",
        "type": "urn:cadmium:chat:read",
        "to": ["@user1@cadmium.org"],
        "payload": {
            "messageID": "7a1b9a72-1677-4476-9b22-ef7fdbcff52e",
        }
    }
    ```

    Response:

    ```json
    {
        "id": "abcd",
        "type": "urn:cadmium:chat:read",
        "from": "cadmium.im",
        "ok": true,
        "payload": {}
    }
    ```

2. Receive notification about message reading on other side:

    ```json
    {
        "id": "defg",
        "type": "urn:cadmium:chat:read",
        "from": "@user2@cadmium.im",
        "ok": true,
        "payload": {
            "messageID": "7a1b9a72-1677-4476-9b22-ef7fdbcff52e"
        }
    }
    ```

### 6.4. Typing message

1. Typing message

    ```json
    {
        "id": "abcd",
        "type": "urn:cadmium:chat:typing",
        "to": ["@user1@cadmium.org"],
    }
    ```

2. Receive notification about typing message on other side:

    ```json
    {
        "id": "defg",
        "type": "urn:cadmium:chat:typing",
        "from": "@user2@cadmium.im",
        "ok": true
    }
    ```
	
### 6.5. Message history API

#### 6.5.1. History retrieving





## 7. Business Rules

### 7.1. Typing message notification

- Client sends typing notification message every second when he is typing. If there is no notifications about typing more than one second then consider that user is stopped the typing.

### 7.2. Message/Media ID

- This is typically Federated Entity ID of message/media (as described and reserved in core specs) which was stored in server's database/content service storage.

## 8. JSON Schema

### Event Entity

```typescript
interface Event {
    id: EntityID; // the message id (stored in chat timeline) - not required when sending
    type: string; // the type of event
    originServerTimestamp: number; // unix timestamp of received message on the origin server (not required when sending)
    content: Content; // the payload of message (depends on type)
}

interface Content {}
```

#### Content sections

- `urn:cadmium:chat:events:types:message`:

    ```typescript
    interface MessageContent : Content {
        text: string; // the text (body) of message
        mentioned?: boolean; // is current entity was mentioned in this message
        media?: Media[]; // media content
		forwardedFrom?: EntityID | string; // entity from which this message was forwarded (may be hidden, so instead client need to show the display name of entity)
		replyTo?: EntityID; // message id to which this message is replying
    }

    interface Media {
        id: string; // media ID
        type: string; // type of media
        size: number; // size of media in bytes
        fileName?: string; // filename of media (with optional extension by dot)
        attrs: MediaAttrs; // media payload (additional attributes)
    }

    // Media attributes definitions

    interface MediaAttrs {}

    // urn:cadmium:chats:media:audio
    interface AudioAttrs : MediaAttrs {
        voice: bool; // whether it is voice message
        duration: number; // duration of audio
        title?: string; // audio title
        artist?: string; // audio artist
        voiceWaveForm?: []byte; // voice wave form
    }

    // urn:cadmium:chats:media:sticker
    interface StickerAttrs : MediaAttrs {
        associatedEmoji: string; // the associated emoji with this 
        stickerSetID: string; // id of sticker set which is associated with this sticker
        isAnimated: boolean; // is client need to animate this sticker
    }

    // urn:cadmium:chats:media:video
    interface VideoAttrs : MediaAttrs {
        isVideoMessage: bool; // is this a rounded video message
        duration: number; // duration of video
        width: number; // width of video
        height: number; // height of video
    }

    // urn:cadmium:chats:media:photo
    interface PhotoAttrs : MeditAttrs {
        width: number; // width of photo
        height: number; // height of photo
    }

	interface GeolocationAttrs : MediaAttrs {
		lat: number; // the GPS latitude
        lon: number; // the GPS longitude
	}
    ```


### Send message (`urn:cadmium:chat:message`)

#### Request

```typescript
interface SendMessageReq {
    type: string;
	content: Content;
}
```

#### Response

```typescript
interface SendMessageResponsePayload {
    id: EntityID; // the message id (stored in chat timeline)
    originServerTimestamp: number; // unix timestamp of sent message on the origin server
}
```

### Read message notification (`urn:cadmium:chat:read`)

#### Request

```typescript
interface ReadMessageNotif {
    eventID: EntityID; // the read message id
}
```

### Edit message notification (`urn:cadmium:chat:message:edit`)

#### Request

```typescript
interface EditMessageNotif {
    eventID: EntityID; // edited message id
    updatedContent: Content; // new content of message
}
```

### Delete message notification (`urn:cadmium:chat:message:delete`)

#### Request

```typescript
interface DeleteMessageNotif {
    eventID: EntityID; // deleted message id
}
```

### Sync history of user chats (`urn:cadmium:chat:history:sync`)

#### Request

```typescript
interface ChatsHistorySyncReq {
    since: EntityID; // event from which need to sync the history
	limit: number; // count of events which need to return
	includeChatInfo: boolean; // include chat/user info mentioned in events into response
	chatID?: EntityID; // get history of specific chat
	direction?: SyncDirection; // in case of getting history of specific chat
}

enum SyncDirection {
	Up = "UP", // previous events from 'since' event
	Down = "DOWN" // next events from 'since' event
}
```

#### Response

```typescript
interface ChatsHistorySyncResp {
    nextBatch: EntityID; // next 'since' value for client
	events: Event[]; // synced events
	chats?: Chat[]; // chats mentioned in events
	users?: User[]; // users mentioned in events
}

interface Event {
	eventID: EntityID; // event id in timeline
	from: EntityID; // event author
	chatID: EntityID; // related chat id
	type: string; // type of event
	timestamp: number; // origin timestamp of event
	originServer: EntityID; // origin server author of event
	content: Content; // content of event
}
```