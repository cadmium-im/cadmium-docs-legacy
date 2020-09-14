# Functional requirements

## General

1. Possibility of federation (via clearnet, yggdrasil, etc.) *(pztrn)*
   - The ability to prohibit, prefer, require SSL for federation. *(pztrn)*
     - For example, for yggdrasil, SSL is redundant, since there each node tries to connect to each node that it communicates with, and SSL creates an unnecessary load. *(pztrn)*
2. Ability to work in p2p mode via yggdrasil (?, undecided)
3. Validation of the sender using cryptographic signatures and keys
    - I suggest doing this optionally, as many users simply don’t need it *(ChronosX88)*
      - I completely agree, and the first implementation should be done without all this. And this item is more suitable for item 4 of this list, it seems *(pztrn)*
4. E2EE (E2E Encryption)
   - e2ee needs to be done "on plugins", especially if we will make gates to other networks. Matrix has OLM, the XMPP has OTR/OMEMO, and so on. A transparent work with all this things should be done for the user. (pztrn)
     - However, it is necessary to choose some standard cryptographic protocol for specs. *(ChronosX88)*
       - This will not work due to the presence of a diverse cryptographic shit zoo. Therefore, you will have to saw it "on plugins", since for the user it should be as transparent as possible. *(pztrn)*
   - e2ee needs to be made as customizable as possible so that it is possible to force/not force e2ee, as well as enable e2ee for individual rooms/chats. *(pztrn)*
5. Ability to serve more than one domain with one server instance
6. Feature-agnostic protocol, that is, the ability to overwrite on top of this all the chat, and the forum, and the blog/microblog.
   - Leave a layer of extensibility, as in XMPP (for developers, of course) *(ChronosX88)*
7. Implement the CEP (Cadmium Enchancement Proposals) mechanism, which will be discussed, and after adoption, enter the main protocol, or go to CER (Cadmium Enchancements Repository).
8. Ability to make transports (bridges, gates) to other IM networks.
   - Here it would be cool to try to push XMPP as the first gate and try to reuse transports from there. *(pztrn)*
     - In this case, we need to make the AppService API, as in the Matrix. *(ChronosX88)*
       - Perhaps only with emphasis on our new protocol. And, again, it seems to me that for transports (or AppService) the use of the HTTP REST API is stupidly redundant, and here you can well use Websockets so as not to make your connection handlers (everything is ready and there), but to reuse the same gorilla (Golang library) and JSON-RPC on top. *(pztrn)*
9. Native clients under all major OS. We can use Go + Qt5, since we are making all free & open source.
   - The question remains with the client for iOS, but if we make a suitable protocol that will work and will allow us to map the Telegram in the form of transport, I have a person who will make the client for iOS :) In general, given the having of a macOS, I can try to do it myself but "somehow later", because my target OS is Windows/Linux/macOS/Android *(pztrn)*

## Chats

1. 1-on-1 chats (with/without saving history)
2. Multi-user (group) chats
3. The ability to easily share files (something like to HTTP Upload XEP from XMPP)
4. Forwards and replies to messages (as in Telegram)
5. Stickers/GIF
6. Message Reactions
7. Ability to create a discussion from a message (threads) as in Slack/Mattermost/Rocket.chat.
8. Ability to create buttons for messages (analog keyboard from Telegram)
   - To do this as an extension, for me it is not quite a *important* thing. *(ChronosX88)*
      - This will help to make the voting poll, for example. And greatly facilitate the transfer of Telegram friends playing games. *(pztrn)*
9. Audio/Video communication at least 1-on-1 (WebRTC/TURN).
   - Group A / V communication is desirable, but given the complexity of "cooking" I suggest at the very end of the priority list. *(pztrn)*
      - I agree, then we can use Jitsi later. (ChronosX88)
10. User channels (as in Telegram)
    - I propose to implement this not as a "subsystem" of the chat, but as a separate entity with the ability to comment and everything you need, in the form of like dislike. In a Telegram, nevertheless, this "crutch" is more than a really suitable system. *(pztrn)*
11. Seamless chat history system (as in Telegram). A simple yet powerful API is **required**.

### Group chats

1. Chat information
   1. Avatar
   2. Description
   3. Members
2. Admin system
   1. Set avatar/group name/description
   2. Kick the user from members
   3. Ban the user for some time (or forever)
   4. Mute the user (restrict sending messages or media)
   5. Set group alias
   6. Set group invite link
   7. Manage the admins (flexible setting the permissions of admins (every aspect))
   8. View the recent actions of admins
   9. Pin the messages
   10. Ability to give name (as position, role) for every admin
3. Ability to set group aliases on other servers
