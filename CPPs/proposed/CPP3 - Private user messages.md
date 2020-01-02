# CPP#3 - Private user messages

## Introduction

This proposal introduces the concept of chats (this proposal describes only private chats) and a mechanism for sending/receiving messages in the chat.

![Message Mindmap](CPP3&#32;-&#32;Message&#32;Mindmap.png)

## Message type identifiers

- `chat:create`: create chat action
- `chat:created`: notification about creating new chat by some user
- `message:send`: send message to chat
- `message:receive`: receive message from chat
- `message:read`: read a message
- `message:receive:read`: receive notification about read message

## Glossary

- **Chat (the so-called "room")** - the entity in which communication occurs between users (participants).
- **Chat history** - an ordered by time list of messages of chat

## Use Cases

Note: C - client, S - server

### Creating private chat

1. C1:

```json
{
    "id": "abcd",
    "type": "chat:create",
    "from": "@user1@cadmium.im",
    "to": "cadmium.im",
    "payload": {
        "chatType": "user",
        "receiverUserId": "@user2@cadmium.im"
    }
}
```

2. S to C1:

```json
{
    "id": "abcd",
    "type": "chat:create",
    "from": "cadmium.im",
    "to": "@user1@cadmium.im",
    "ok": true,
    "payload": {
        "roomId": "!22c5d942-22bc-45b3-9541-ec2b49afe5ec@cadmium.im"
    }
}
```

Error response (S):

```json
{
    "id": "abcd",
    "type": "chat:create",
    "from": "cadmium.im",
    "to": "@user1@cadmium.im",
    "ok": false,
    "payload": {
        "errCode": "blacklistedUser",
        "errText": "You are blacklisted for this user"
    }
}
```

3. S to C2:

```json
{
    "id": "abcd",
    "type": "chat:created",
    "from": "cadmium.im",
    "to": "@user2@cadmium.im",
    "ok": true,
    "payload": {
        "roomId": "!22c5d942-22bc-45b3-9541-ec2b49afe5ec@cadmium.im",
        "creatorUserId": "@user1@cadmium.im"
    }
}
```

### Sending message to chat

1. C1:

```json
{
    "id": "abcd",
    "type": "message:send",
    "from": "@user1@cadmium.im",
    "to": "!22c5d942-22bc-45b3-9541-ec2b49afe5ec@cadmium.im",
    "payload": {
        "messageType": "text",
        "messageText": "Hello world!"
    }
}
```

2. S to C1:

```json
{
    "id": "abcd",
    "type": "message:send",
    "from": "cadmium.im",
    "to": "@user1@cadmium.im",
    "ok": true,
    "payload": {
        "messageTimestamp": 1577988125,
        "messageId": "ee55b940-0f2f-46dc-8c8e-e6ea5874c3c2"
    }
}
```

3. S to C2:

```json
{
    "id": "abcd",
    "type": "message:receive",
    "from": "cadmium.im",
    "to": "@user2@cadmium.im",
    "ok": true,
    "payload": {
        "messageType": "text",
        "messageId": "ee55b940-0f2f-46dc-8c8e-e6ea5874c3c2",
        "messageText": "Hello world!"
    }
}
```

### Reading message

1. C2:

```json
{
    "id": "abcd",
    "type": "message:read",
    "from": "@user2@cadmium.im",
    "to": "!22c5d942-22bc-45b3-9541-ec2b49afe5ec@cadmium.im",
    "payload": {
        "messageId": "ee55b940-0f2f-46dc-8c8e-e6ea5874c3c2"
    }
}
```

2. S to C2:

```json
{
    "id": "abcd",
    "type": "message:read",
    "from": "cadmium.im",
    "to": "@user2@cadmium.im",
    "ok": true,
    "payload": {}
}
```

3. S to C1:

```json
{
    "id": "abcd",
    "type": "message:receive:read",
    "from": "cadmium.im",
    "to": "@user1@cadmium.im",
    "ok": true,
    "payload": {
        "messageId": "ee55b940-0f2f-46dc-8c8e-e6ea5874c3c2",
        "readUserId": "@user2@cadmium.im"
    }
}
```

## Errors

- Ratelimit system: enabled
- Authentication required: true
- `blacklistedUser`: user 1, who is trying to create a chat, is blacklisted by user 2

## Business Rules

- Every message MUST be recorded in chat message history
- Timestamp MUST be in UTC timezone
- Server MUST store read state of message as list of user IDs which read this message

## Security Considerations

None.

## JSON Schema

### Creating chat payload

- Request for creating chat:

```typescript
interface CreatePrivateChatRequestPayload {
    /**
     * Type of chat which need to create
     */
    chatType: ChatType,

    /**
     * Second chat member ID (first member is who trying to create this chat) in
     * case when chatType is "user"
     */
    receiverUserId: EntityID
}
```

- Response for creating chat:

```typescript
interface CreatePrivateChatResponsePayload {
    /**
     * Identifier of newly created chat
     */
    roomId: EntityID
}
```

- Notification for second member of newly created chat about the fact of creating this chat

```typescript
interface CreatePrivateChatNotificationPayload {
    /**
     * Identifier of newly created chat
     */
    roomId: EntityID,

    /**
     * Identifier of user which created this chat
     */
    creatorUserId: EntityID
}
```

### Sending message to chat payload

- Send message action request payload

```typescript
interface SendMessageRequestPayload {
    /**
     * Type of the message
     */
    messageType: MessageType,

    /**
     * Text of the message
     */
    messageText: string,


    /**
     * URL of file/image/voice which attached to this message (in case of
     * appropriate message type)
     */
    fileUrl?: string
}
```

- Send message action response payload

```typescript
interface SendMessageResponsePayload {
    /**
     * UNIX Timestamp of newly sent message
     */
    messageTimestamp: number,

    /**
     * Message identifier in chat history of newly sent message
     */
    messageId: string
}
```

- Notification about new message in the chat

```typescript
interface NewMessageNotificationPayload {
    /**
     * Identifier of received message in chat history
     */
    messageId: string,

    /**
     * UNIX timestamp of received message
     */
    messageTimestamp: number,

    /**
     * Text of received message
     */
    messageText: string
}
```

### Reading message payload

- Read message action request payload

```typescript
interface ReadMessageRequesPayload {
    /**
     * Identifier of message which need mark as read
     */
    messageId: string,
}
```

- Notification about reading message

```typescript
interface ReadMessageRequesPayload {
    /**
     * Identifier of message which was read
     */
    messageId: string,

    /**
     * Identifier of user which read this message
     */
    readUserId: EntityID
}
```