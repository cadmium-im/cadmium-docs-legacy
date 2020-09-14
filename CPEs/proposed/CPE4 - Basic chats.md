# CPE#4 - Basic chats

## 1. Introduction

This extension is intended for organizing chats between some entities.

`// TODO describe about events`

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

- `urn:cadmium:chats:message-types:general` - message with text and optional media
- `urn:cadmium:chats:message-types:audio` - audio message
- `urn:cadmium:chats:message-types:geolocation` - message with geolocation (coordinates)

## 6. Use cases

### 6.1. Exchanging messages

1. Sending message:

    ```json
    {
        "id": "abcd",
        "type": "urn:cadmium:chats:message",
        "to": ["@user2@cadmium.org"],
        "payload": {
            "type": "urn:cadmium:chats:message-types:general",
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
            "type": "urn:cadmium:chats:message-types:general",
            "content": {
                "text": "hello world",
            }
        }
    }
    ```

### 6.2. Exchanging media

1. Upload the media to HTTP Upload service (explanation out of scope of this document)
2. Send message containing the URL of uploaded media:

   ```json
    {
        "id": "abcd",
        "type": "urn:cadmium:chats:message",
        "to": ["@user2@cadmium.org"],
        "payload": {
            "type": "urn:cadmium:chats:message-types:general",
            "content": {
                "text": "",
                "media": [
                    {
                        "url": "https://upload.cadmium.im/4a771ac1-f0b2-4a4a-9700-f2a26fa2bb67",
                        "mimeType": "image/jpeg"
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
        "from": "cadmium.org",
        "ok": true,
        "payload": {
            "messageID": "7a1b9a72-1677-4476-9b22-ef7fdbcff52e",
            "originServerTimestamp": 1599329232
        }
    }
   ```

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
            "type": "urn:cadmium:chats:message-types:general",
            "content": {
                "text": "",
                "media": [
                    {
                        "url": "https://upload.cadmium.im/4a771ac1-f0b2-4a4a-9700-f2a26fa2bb67",
                        "mimeType": "image/jpeg"
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
        "type": "urn:cadmium:chats:read",
        "from": "@user2@cadmium.im",
        "ok": true,
        "payload": {}
    }
    ```

## 7. Business Rules

### 7.1. Typing message notification

- Client sends typing notification message every second when he is typing. If there is no notifications about typing more than one second then consider that user is stopped the typing.

## 8. JSON Schema

### Send message (`urn:cadmium:chats:message`)

**Request**:

```typescript
interface SendMessagePayload {
    type: string; // the type of message
    reply?: string; // message id on which this message is replying
    content: Content // the payload of message (depends on type)
}
```

**Response**:

```typescript
interface SendMessageResponsePayload {
    messageID: string; // the message id (stored in chat timeline)
    originServerTimestamp: number // unix timestamp of sent message on the origin server
}
```

#### Content sections

- `urn:cadmium:chats:message-types:general`:

    ```typescript
    interface GeneralMessageContent {
        text: string; // the text (body) of message
        media?: Media[]; // media content
    }

    interface Media {
        url: string; // url of media (where it stores)
        mimeType: string; // mime type of media
    }
    ```

- `urn:cadmium:chats:message-types:audio`:

    ```typescript
    interface AudioMessageContent {
        url: string; // the url of audio message
    }
    ```

- `urn:cadmium:chats:message-types:geolocation`:

    ```typescript
    interface GeolocationMessageContent {
        lat: number; // the GPS latitude
        lon: number; // the GPS longitude
    }
    ```

### Receive message (`urn:cadmium:chats:message`)

```typescript
interface ReceiveMessagePayload {
    messageID: string; // the id of received message
    originServerTimestamp: number; // unix timestamp of received message on the origin server
    type: string; // the type of message
    reply?: string; // message id on which this message is replying
    content: Content; // the payload of message (depends on type)
}
```

### Read message (`urn:cadmium:chats:read`)

**Request**:

```typescript
interface ReadMessagePayload {
    messageID: string; // the message id of read message
}
```

**Response**:

None.

### Receive read message notification (`urn:cadmium:chats:read`)

```typescript
interface ReceiveReadMessageNotifPayload {
    messageID: string; // the message id of read message
}
```