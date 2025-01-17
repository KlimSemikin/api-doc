# Webhooks

Webhooks is a way to notify you about new messages or other events in our system.
It's mandatory to use webhooks for 99% integrations with our API.

Endpoint with <code>webhook_url</code> **must** response with http status code `200` if endpoint received webhook.

Webhook could be repeated up to 10 times within 60 sec delay if response status code is not equal 200. After 10 retries webhook will be dropped and nevery delivered.

<aside class="notice">
The configuration of the name display settings does not affect webhooks in any way.
</aside>

## New conversation

> The above command returns JSON structured like this:

```json
{
   "type":"conversation",
   "event":"new",
   "data":{
      "external_id":1,
      "name":"Sender Name",
      "avatar_url": "https://cdn.pact.im/conversations/avatars/000/000/000/original/open-uri00000000-00000-0p0igg0?0000000000",
      "channel_id":1,
      "channel_type":"whatsapp",
      "created_at":"2017-11-11 12:45:53 UTC",
      "created_at_timestamp":1603118584,
      "last_message_at":null,
      "last_income_message_at":null,
      "sender_external_id":"79250000001"
   }
}

```

### When

 - Client sends new message first time.
 - You sent message to new client. Message was delivered, conversation was created.

<aside class="notice">
   <code>sender_external_id</code> attribute is available only for whatsapp and instagram conversations.
</aside>


## Update conversation

> The above command returns JSON structured like this:

```json
{
   "type":"conversation",
   "event":"update",
   "data":{
      "external_id":1,
      "name":"Sender Name",
      "avatar_url": "https://cdn.pact.im/conversations/avatars/000/000/000/original/open-uri00000000-00000-0p0igg0?0000000000",
      "channel_id":1,
      "channel_type":"whatsapp",
      "created_at":"2017-11-11 12:45:53 UTC",
      "created_at_timestamp":1603118584,
      "last_message_at":null,
      "last_income_message_at":null,
      "sender_external_id":"79250000001"
   }
}

```

### When

- When conversation with such an sender_external_id already exists and new data is received the sender_name and sender_avatar_url.

<aside class="notice">
   <code>sender_external_id</code> attribute is available only for whatsapp and instagram conversations.
</aside>

## New message

> The above command returns JSON structured like this:

```json
{
   "type":"message",
   "event":"new",
   "data":{
      "external_id":1,
      "external_public_id":1,
      "channel_id":1,
      "channel_type":"whatsapp",
      "channel":{
        "id": 1,
        "type": "whatsapp"
      },
      "conversation_id":1,
      "message":"Message body",
      "location": {},
      "income":true,
      "created_at":"2017-11-11 12:45:53 UTC",
      "created_at_timestamp":1603118584,
      "ack":1,
      "attachments":[],
      "job_id":1
   }
}

```

### When

 - Client sent new message.
 - You sent message to client.

<aside class="success">
Webhook will be sent ONLY when message received/delivered.
</aside>

<aside class="notice">
<code>job_id</code> attribute is available only in async delivery mode.
</aside>

## Message delivery/read status

> The above command returns JSON structured like this:

```json
{
    "type": "message",
    "event": "ack",
    "data": {
        "external_id": 1,
        "channel": {
            "id": 1,
            "type": "whatsapp"
        },
        "conversation_id": 1,
        "ack": 3
    }
}

```

### When

 - Message was delivered
 - Message was read

Possible statuses:

  - `-1` : Message is in `preparing to deliver` state
  - ` 0` : Message is in `delivering` state
  - ` 1` : Message was sent
  - ` 2` : Message was delivered
  - ` 3` : Message was read

If message in a `-1` state - message may not be delivered. You must to care about message delivering by yourself.

## Delivery job executed

> The above command returns JSON structured like this:

```json
{
   "type":"job",
   "event":"executed",
   "data":{
      "id":1,
      "result":"DELIVERED",
      "message_id":1,
      "date":1510404738
   }
}

```

### When

 - You sent message in async mode. Delivery job was executed and
   delivery result is known.

Possible results:

  - `DELIVERED`
  - `NOT DELIVERED`

If message is `NOT DELIVERED` we append `reason` filed with reason information.

## Whatsapp: new QR-code

> The above command returns JSON structured like this:

```json
{
   "type":"qr_code",
   "event":"new",
   "company_id":1,
   "channel_id":1,
   "data":"BASE64 QR-CODE image string"
}

```

### When

  - You are connecting `whatsapp` channel in your company
  - User logged out in WhatsApp app

## Whatsapp: channel connected sucessfuly

> The above command returns JSON structured like this:

```json
{
   "type":"system",
   "severity":"information",
   "data":{
      "message":"authorized",
      "date":"2017-11-11 12:45:53 UTC",
      "date_timestamp":1603118584,
      "timestamp":1603119138,
      "details":{
         "entity":"channel",
         "entity_id":1
      }
   }
}

```

### When

QR-code was scanned successfuly

<aside class="notice">
<code>date_timestamp</code> is date timestamp;
<code>timestamp</code> is callback timestamp.
</aside>

## Whatsapp: phone offline

> The above command returns JSON structured like this:

```json
{
   "type":"system",
   "severity":"critical",
   "data":{
      "message":"phone offline",
      "date":"2017-11-11 12:45:53 UTC",
      "date_timestamp":1603118584,
      "timestamp":1603119138,
      "details":{
         "entity":"channel",
         "entity_id":1
      }
   }
}

```

### When

Device with WhatsApp application is unreachable. We can't work with whatsapp while phone is offline.

<aside class="notice">
<code>date_timestamp</code> is date timestamp;
<code>timestamp</code> is callback timestamp.
</aside>

## Whatsapp: phone online

> The above command returns JSON structured like this:

```json
{
   "type":"system",
   "severity":"critical",
   "data":{
      "message":"phone online",
      "date":"2017-11-11 12:45:53 UTC",
      "date_timestamp":1603118584,
      "timestamp":1603119138,
      "details":{
         "entity":"channel",
         "entity_id":1
      }
   }
}

```

### When

Device with WhatsApp application is reachable again.

<aside class="notice">
<code>date_timestamp</code> is date timestamp;
<code>timestamp</code> is callback timestamp.
</aside>

## Whatsapp: channel not available

> The above command returns JSON structured like this:

```json
{
   "type":"system",
   "severity":"critical",
   "data":{
      "message":"unavailable",
      "date":"2017-11-11 12:45:53 UTC",
      "date_timestamp":1603118584,
      "timestamp":1603119138,
      "details":{
         "entity":"channel",
         "entity_id":1
      }
   }
}

```

### When

Someone started session at [web.whatsapp.com](https://web.whatsapp.com) or user similar integration.
We can't work with whatsapp while Whatsapp WEB is open.

<aside class="notice">
<code>date_timestamp</code> is date timestamp;
<code>timestamp</code> is callback timestamp.
</aside>

## Whatsapp: channel disabled

> The above command returns JSON structured like this:

```json
{
   "type":"system",
   "severity":"critical",
   "data":{
      "message":"disabled",
      "date":"2017-11-11 12:45:53 UTC",
      "date_timestamp":1603118584,
      "timestamp":1603119138,
      "details":{
         "entity":"channel",
         "entity_id":1
      }
   }
}

```

### When

By some reason whatsapp session is not alive anymore (for example, user exited on the device or whatsapp expired session)

<aside class="notice">
<code>date_timestamp</code> is date timestamp;
<code>timestamp</code> is callback timestamp.
</aside>

## Whatsapp: trying to resume channel work

> The above command returns JSON structured like this:

```json
{
   "type":"system",
   "severity":"critical",
   "data":{
      "message":"trying_resume_work",
      "date":"2017-11-11 12:45:53 UTC",
      "date_timestamp":1603118584,
      "timestamp":1603119138,
      "details":{
         "entity":"channel",
         "entity_id":1
      }
   }
}

```

### When

We're trying to resume work after previous conflict state

<aside class="notice">
<code>date_timestamp</code> is date timestamp;
<code>timestamp</code> is callback timestamp.
</aside>

## Whatsapp: synchronization completed

> The above command returns JSON structured like this:

```json
{
   "type":"system",
   "severity":"critical",
   "data":{
      "message":"synchronized",
      "date":"2017-11-11 12:45:53 UTC",
      "date_timestamp":1603118584,
      "timestamp":1603119138,
      "details":{
         "entity":"channel",
         "entity_id":1
      }
   }
}

```

### When

  - Whatsapp channel was connected and syncronisation was completed
  - Syncronisation after work resume

<aside class="notice">
<code>date_timestamp</code> is date timestamp;
<code>timestamp</code> is callback timestamp.
</aside>

## Instagram: changed state to disabled

> The above command returns JSON structured like this:

```json
{
   "type":"system",
   "severity":"information",
   "data":{
      "message":"IG was set DISABLED",
      "date":"2017-11-11 12:45:53 UTC",
      "date_timestamp":1603118584,
      "details":{
         "entity":"channel",
         "entity_id":1
      }
   }
}

```

### When

  - Service message successfully delivered
  - Service message delivery failed


## Group chats: new conversation

> The above command returns JSON structured like this:

```json
{
   "type": "group_conversation",
   "event": "new",
   "data":{
      "name": "Group name",
      "avatar_url": null,
      "channel_id": 1,
      "created_at": "2023-04-28T12:34:01.937Z",
      "external_id": 1,
      "channel_type": "whatsapp",
      "last_message_at": null,
      "sender_external_id": "120363148147828952",
      "created_at_timestamp": 1682685241,
      "last_income_message_at": null
   }
}

```

### When

 - Group chat member sends new message first time.
 - You send a message to a group chat. Message was delivered, conversation was created.


## Group chats: new group message

> The above command returns JSON structured like this:

```json
{
    "type": "group_message",
    "event": "new",
    "data": {
        "ack": 0,
        "income": true,
        "channel": {
            "id": 1,
            "type": "whatsapp"
        },
        "message": "Message body",
        "location": null,
        "channel_id": 1,
        "created_at": "2023-04-05T12:00:52.000Z",
        "attachments": [],
        "external_id": 1,
        "channel_type": "whatsapp",
        "conversation_id": 1,
        "external_public_id": "79000000000",
        "created_at_timestamp": 1680696052,
        "external_created_at_timestamp": 1680696052
    }
}

```

### When

 - Group chat member sent new message.
 - You send a message to a group chat.


## Group chats: message delivery/read status

> The above command returns JSON structured like this:

```json
{
   "type": "group_message",
   "event": "ack",
   "data": {
      "channel": {
         "id": 65706,
         "type": "whatsapp"
      },
      "timestamp": 1682658436,
      "external_id": 1,
      "participant": "79000000000",
      "conversation_id": 1,
      "external_public_id": null,
      "ack": 3
   }
}

```

### When

 - Message was delivered
 - Message was read

Possible statuses:

  - ` 0` : Message is in `delivering` state
  - ` 1` : Message was sent
  - ` 2` : Message was delivered
  - ` 3` : Message was read


## Group chats: new active member

> The above command returns JSON structured like this:

```json
{
   "type": "group_conversation_contact",
   "event": "new",
   "data": {
      "contact": {
         "external_public_id": "79000000000"
      },
      "channel_id": 65706,
      "channel_type": "whatsapp",
      "date_timestamp": 1682652148,
      "conversation_id": 1
   }
}

```

### When

 - Group chat member sent new message first time.


