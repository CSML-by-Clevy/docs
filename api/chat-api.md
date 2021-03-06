---
description: Interact with your bot using the Chat API
---

# Chat API

## POST /chat

The Chat API allows you to send chat requests to your bot on behalf of an end user. You can send a text request, and receive the bot's response in return.

This API works synchronously by default: you will receive all of the bot's messages in one array in response to your request.

In async mode, the CSML server will send messages as they are processed by the bot, asynchronously, to an endpoint of your choice.

### Sending a chat request

You can query the Chat API by sending a simple text message for analysis. This will either continue any previous conversation or start a new one.

```bash
# replace the x-api-key and x-api-signature values with your data
curl -X "POST" "https://clients.csml.dev/prod/api/chat" \
     -H 'content-type: application/json' \
     -H 'accept: application/json' \
     -H 'x-api-key: ${API_KEY}|${TIMESTAMP}' \
     -H 'x-api-signature: sha256=HMAC_SHA256(${API_KEY}|${TIMESTAMP}, ${API_SECRET}, "hex")' \
     -d $'{
            "client": {
              "user_id": "some-user-id"
            },
            "metadata": {
              "firstname": "John",
              "lastname": "Doe"
            },
            "request_id": "random-id",
            "payload": {
              "content": {
                "text": "Hello"
              },
              "content_type": "text"
            }
          }'
```

#### Valid request message payloads

The following message types are accepted \(see below for payload examples\): `text`, `image`, `video`, `audio`, `file`, `url`, `payload`, `flow_trigger`.

Additional properties can be added to the message's `content` and will be available as `event.my_added_prop` in the context of the CSML.

#### Triggering a specific flow

To trigger a specific flow, you can send the following event:

```javascript
{
  "client": {
    "user_id": "some-user-id"
  },
  "metadata": {
    "key": "value",
    "otherkey": "othervalue"
  },
  "request_id": "random-id",
  "payload": {
    "content": {
      "flow_id": "some-flow-id",
      "close_flows": true
    },
    "content_type": "flow_trigger"
  }
}
```

You can also choose to force-close any previously open conversation with `close_flows`. If you don't close previous conversations and the flow is a recursive flow \(i.e has no `hold` keywords\), then the previous conversation will be relaunched at the last available step in that flow.

### Request body

| Name | Type | Description |
| :--- | :--- | :--- |
| \*metadata | `object` | Key-value pairs of metadata to inject into the conversation |
| request\_id | `string` | A random client-issued string for tracing requests. If none is provided, will be automatically generated. |
| callback\_url | `string` | Endpoint to POST the bot's messages in response to your request. |
| \*payload | `object` |  |
| \*payload.content\_type | `string` | one of "text" or "flow\_trigger" |
| \*payload.content | `object` |  |
| payload.content.text | `string` | the string to send as the request text |
| payload.content.flow\_id | `string` | the id of the flow to trigger \("flow\_trigger" type only\) |
| payload.content.close\_flows | `boolean` | force close any currently open conversation \("flow\_trigger" type only\) |
| \*client | `object` |  |
| \*client.user\_id | `string` | the user's unique identifier |

```javascript
{
  "client": {
    "user_id": "some-user-id"
  },
  "metadata": {
    "key": "value",
    "otherkey": "othervalue"
  },
  "request_id": "random-id",
  "payload": {
    "content": {
      "text": "Hello"
    },
    "content_type": "text"
  }
}
```

### Response body

| Name | Type | Description |
| :--- | :--- | :--- |
| request\_id | `string` | A random client-issued string for tracing requests. If none is provided, will be automatically generated |
| interaction\_id | `string` | A random server-generated ID for the interaction |
| \*messages | `array` | All the messages to send to the client in order |
| \*message.payload | `object` | See `Message payloads` below |
| \*client | `object` |  |
| \*client.bot\_id | `string` | the bot's ID |
| \*client.channel\_id | `string` | the channels's ID |
| \*client.user\_id | `string` | the user's unique identifier |
| \*received\_at | `string` | UTC time at which the message was received by the CSML server |
| \*is\_authorized | `boolean` | whether or not the user is authorized to query the chat |

```javascript
{
  "request_id": "045338c4-f214-4f94-b1a7-3255b3d70f3c",
  "interaction_id": "47ead3a7-1614-4dac-8f85-ebbd2dbc0123",
  "callback_url": "https://example.com",
  "messages": [
    {
      "conversation_id": "f2461d23-6069-4f78-ae81-f127e4c3d9a0",
      "direction": "SEND",
      "interaction_order": 0,
      "payload": {
        "content": {
          "text": "Some message"
        },
        "content_type": "text"
      }
    }
  ],
  "client": {
    "bot_id": "b797a3b6-ad8c-446c-acfe-dfcafd787f4e",
    "channel_id": "dd446008-3768-41df-9be9-f6ea0371f920",
    "user_id": "some-user-id"
  },
  "received_at": "2019-11-16T17:48:26.519Z",
  "is_authorized": true
}
```

### Valid message payloads

CSML message components all have a matching message format for client use.

```javascript
> Text()
{
  "content": {
    "text": "message"
  },
  "content_type": "text"
}
```

```javascript
> Typing()
{
  "content": {
    "duration": 1000
  },
  "content_type": "typing"
}
```

```javascript
> Wait()
{
  "content": {
    "duration": 1000
  },
  "content_type": "wait"
}
```

```javascript
> Url()
{
  "content": {
    "url": "https://example.com",
    "title": "title",
    "text": "text"
  },
  "content_type": "url"
}
```

```javascript
> Image()
{
  "content": {
    "url": "https://example.com/image.jpg",
  },
  "content_type": "image"
}
```

```javascript
> Audio()
{
  "content": {
    "url": "https://example.com/audio.mp3",
  },
  "content_type": "audio"
}
```

```javascript
> Video()
{
  "content": {
    "url": "https://example.com/video.mp4",
  },
  "content_type": "video"
}
```

```javascript
> File()
{
  "content": {
    "url": "https://example.com/video.mp4",
  },
  "content_type": "file"
}
```

```javascript
> Button()
{
  "content": {
    "title": "Button title",
    "payload": "Button payload"
  },
  "content_type": "button"
}
```

```javascript
> Payload()
{
  "content": {
    "payload": "Custom payload"
  },
  "content_type": "payload"
}
```

```javascript
> Question()
{
  "content": {
    "title": "Question",
    "buttons": [Button]
  },
  "content_type": "question"
}
```

### Imbricated components

In cases where components are imbricated, the `content` wrapper is only applied for the top-most element.

For example: 

```cpp
// CSML
say Question(
    "Where is Brian",
    buttons = [
        Button("In the kitchen"),
        Button("Somewhere else"),
    ]
)

// JSON output
{
    "content": {
    "title": "Question",
    "buttons": [
      {
        "content_type": "button",
        "title": "In the kitchen",
      },
      {
        "content_type": "button",
        "title": "Somewhere else",
      },
    ],
  },
  "content_type": "video"
}
```

