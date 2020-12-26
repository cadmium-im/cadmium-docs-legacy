# CPE#4 - Basic chats

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

- `urn:cadmium:chats:message` - regular message
- `urn:cadmium:chats:read` - read message system
- `urn:cadmium:chats:typing` - typing message system

## 4. Errors

- Ratelimit system: disabled
- Authorization: enabled

### 4.1. Error types

- `urn:cadmium:chats:private:banned` - Sending messages to user is prohibited, because he banned the sender of current message - in case if we send the message to user entity.

## 5. Chat message types

- `urn:cadmium:chats:message:types:general` - message with text and optional media
- `urn:cadmium:chats:message:types:geolocation` - message with geolocation (coordinates)

### 5.1. Media types

- `urn:cadmium:chats:media:photo` - a photo
- `urn:cadmium:chats:media:audio` - an audio or voice message
- `urn:cadmium:chats:media:video` - a video or rounded video message
- `urn:cadmium:chats:media:file` - any other file type
- `urn:cadmium:chats:media:sticker` - a sticker
- `urn:cadmium:chats:media:gif` - an animated GIF 

## 6. Use cases

### 6.1. Exchanging messages

1. Sending message:

    ```json
    {
        "id": "abcd",
        "type": "urn:cadmium:chats:message",
        "to": ["@user2@cadmium.org"],
        "payload": {
            "type": "urn:cadmium:chats:message:types:general",
            "content": {
                "text": "hello world",
            }
        }
    }
    ```

    **Successful response**:

    ```json
    {
        "id": "abcd",
        "type": "urn:cadmium:chats:message",
        "from": "cadmium.org",
        "ok": true,
        "payload": {
            "messageID": "3b5135a5-aff5-4396-a629-a254f383e82f",
            "originServerTimestamp": 1599327584
        }
    }
    ```

    **Error response**:

    ```json
    {
        "id": "abcd",
        "type": "urn:cadmium:chats:message",
        "from": "cadmium.org",
        "ok": false,
        "payload": {
            "errID": "urn:cadmium:chats:private:banned",
            "errText": "The user banned you"
        }
    }
    ```

2. Receiving message on other side:

    ```json
    {
        "id": "abcd",
        "type": "urn:cadmium:chats:message",
        "from": "@user1@cadmium.im",
        "ok": true,
        "payload": {
            "messageID": "3b5135a5-aff5-4396-a629-a254f383e82f",
            "originServerTimestamp": 1599327584,
            "type": "urn:cadmium:chats:message:types:general",
            "content": {
                "text": "hello world",
            }
        }
    }
    ```

### 6.2. Exchanging media

1. Upload the media to Content Service (explanation out of scope of this document)
2. Send message containing the metadata of uploaded media:

   ```json
    {
        "id": "abcd",
        "type": "urn:cadmium:chats:message",
        "to": ["@user2@cadmium.im"],
        "payload": {
            "type": "urn:cadmium:chats:message-types:general",
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

   **Response**:

   ```json
   {
        "id": "abcd",
        "type": "urn:cadmium:chats:message",
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

   ```json
   {
        "id": "defg",
        "type": "urn:cadmium:chats:message",
        "from": "@user1@cadmium.im",
        "ok": true,
        "payload": {
            "messageID": "7a1b9a72-1677-4476-9b22-ef7fdbcff52e",
            "originServerTimestamp": 1599329232,
            "type": "urn:cadmium:chats:message:types:general",
            "content": {
                "text": "",
                "media": [
                    {
                        "id": "4a771ac1-f0b2-4a4a-9700-f2a26fa2bb67@content.cadmium.im",
                        "type": "urn:cadmium:chats:media:photo",
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
        "type": "urn:cadmium:chats:read",
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
        "type": "urn:cadmium:chats:read",
        "from": "cadmium.im",
        "ok": true,
        "payload": {}
    }
    ```

2. Receive notification about message reading on other side:

    ```json
    {
        "id": "defg",
        "type": "urn:cadmium:chats:read",
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
        "type": "urn:cadmium:chats:typing",
        "to": ["@user1@cadmium.org"],
        "payload": {}
    }
    ```

2. Receive notification about typing message on other side:

    ```json
    {
        "id": "defg",
        "type": "urn:cadmium:chats:typing",
        "from": "@user2@cadmium.im",
        "ok": true,
        "payload": {}
    }
    ```

## 7. Business Rules

### 7.1. Typing message notification

- Client sends typing notification message every second when he is typing. If there is no notifications about typing more than one second then consider that user is stopped the typing.

### 7.2. Message/Media ID

- This is typically Federated Entity ID of message/media (as described and reserved in core specs) which was stored in server's database/content service storage.

## 8. JSON Schema

### Message (`urn:cadmium:chats:message`)

**Request**:

```typescript
interface MessagePayload {
    messageID?: EntityID; // the message id (stored in chat timeline) - not required when sending
    type: string; // the type of message
    replyTo?: EntityID; // message id to which this message is replying
    originServerTimestamp?: number; // unix timestamp of received message on the origin server (not required when sending)
    forwardedFrom?: EntityID | string; // entity from which this message was forwarded (may be hidden, so instead client need to show the display name of entity)
    content: Content // the payload of message (depends on type)
}
```

**Response**:

```typescript
interface SendMessageResponsePayload {
    messageID: EntityID; // the message id (stored in chat timeline)
    originServerTimestamp: number; // unix timestamp of sent message on the origin server
}
```

#### Content sections

- `urn:cadmium:chats:message:types:general`:

    ```typescript
    interface GeneralMessageContent {
        text: string; // the text (body) of message
        mentioned?: boolean; // is current entity was mentioned in this message
        media?: Media[]; // media content
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
    ```

- `urn:cadmium:chats:message:types:geolocation`:

    ```typescript
    interface GeolocationMessageContent {
        lat: number; // the GPS latitude
        lon: number; // the GPS longitude
    }
    ```

### Read message notification (`urn:cadmium:chats:read`)

```typescript
interface ReadMessageNotifPayload {
    messageID: EntityID; // the message id of read message
}
```