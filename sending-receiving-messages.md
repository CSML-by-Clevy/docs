# Sending and Receiving Messages

CSML is able to handle many types of messages by default.

To send a message to the end user, simply use the keyword `say` followed by the message type you want to send. 

```cpp
somestep:
  say "I'm a text message"
  say Wait(1500)
  say Text("I'm also a text message")
  say Typing(1500)
  say Url("https://clevy.io")
  say Image("https://media.giphy.com/media/ASd0Ukj0y3qMM/source.gif")
  say Video("https://www.youtube.com/watch?v=DLzxrzFCyOs")
  say Audio("https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/253508261")
  say Question(
    title = "What is love?",
    buttons = [
      Button("baby don't hurt me"),
      Button("don't hurt me"),
      Button("no more")
    ]
  )
  say Carousel(
    cards = [
      Card(
        title="title",
        subtitle="an optional description",
        image_url="https://images.unsplash.com/photo-1567322329435-28658f2958c4",
        buttons=[Button("click me!")]
      )
    ]
  )
```

## Styling text

You can style text using Markdown. Some channels however only partially support Markdown \(Messenger for example only allows bold, italic, strikethrough, but not tables\), so don't get too fancy there.

#### Quick Markdown reference

```cpp
# Title 1
## Title 2

**bold**
*italic*
~strikethrough

[link text](https://google.com)
```

## Message types

Below is a list of default valid message components, which are automatically converted to nicely formatted messages for the channel in which the user is talking with the bot.

| name | description | fallback |
| :--- | :--- | :--- |
| Text\(string\) | Output a simple string \(with no styling attached\). This is also the default type, alias of `say "string"`. This component supports Markdown on channels that allow text formatting. |  |
| Wait\(number\) | Wait `number` milliseconds before sending the next message |  |
| Typing\(number\) | Display a typing indicator for `number` milliseconds | Wait\(number\) |
| Url\(string, text="text", title="title"\) | Format `string` as the URL. Optionally, the text and title parameters can provide a way to make "nicer" links, where supported. | Text\(string\) |
| Image\(string\) | Display the image available at URL `string` | Text\(string\) |
| Video\(string\) | Display the video available at URL `string`. Supports Youtube, Dailymotion and Vimeo URLs, or mp4 and ogg. | Url\(string\) |
| Audio\(string\) | Display the audio available at URL `string`. Supports Soundcloud embed links, or mp3, wav and ogg. | Url\(string\) |
| Button\(string\) | Display `string` inside a clickable button | Text\(string\*\) |
| Question\(title = string\(, buttons = \[Button\]\)\) | Display a list of buttons with a header of `string`. Title parameter is optional. | Text\(string\) + list of buttons |
| Card\(title="string", buttons=\[Button\], image\_url="string"\) | Display nicely formatted content in a `Carousel` component \(on supported channels\). Only title is mandatory. | Question\(title, buttons\) or Text\(title\) |
| Carousel\(cards=\[Card\]\) | Display a list of `Card`components in a carousel \(on supported channels\). The cards parameter is mandatory. | \_\_ |

{% hint style="info" %}
Components may receive additional optional parameters to accommodate the needs of certain channels.

For example, in the Messenger channel you can add a `button_type="quick_reply"` parameter to the `Question` component to provide a different type of buttons.
{% endhint %}

## Asking Questions

A conversation with a chatbot is not very different from a human-to-human dialogue: sometimes the user talks, sometimes the bot talks.

CSML provides a solution when you need the chatbot wait for the user's input: using the `hold` keyword, the chatbot will remember its position in the conversation and simply wait until the user says something, then continue from there.

```cpp
somestep:
  say Question("Do you like cheese?", buttons=[Button("yes"), Button("no")])
  
  hold

  if (event == "yes") say "I'm glad to know that you like cheese!"
  else say "Oh that's too bad!"
```

## Receiving messages

When receiving an event from an end-user, the CSML interpreter will try to:

1. match it with a new flow,
2. or consume it in the existing conversation,
3. or trigger the default flow as defined in the bot settings,

by order of priority.

CSML Events are made available in the script when a step is triggered by a user's input. The `event` object is a complex structure which contains a lot more than just the string that was typed by the user. It contains a lot of information that is used by the CSML interpreter, for example the custom button payload to match a list of choices offered to the user. This makes it easy to handle both a click on a "OK" button or the user typing the word "yes".

By default, events are only expected when:

* the flow was just triggered, the current step being triggered is `start`
* the bot asked something to the user, and is waiting for the user's input at the same step

In those cases, a local variable, `event`, is made available, with the content of the user request. When no event is available, event is set to `NULL`.

## ⚠️ Limitations

{% hint style="danger" %}
The maximum theoretical size of a `say` payload is **16KB**. However, each channel will have different limitations depending on the type of component. For a Text component for example, most channels are limited to a few hundred characters for. 

Please refer to each channel's official documentation to find out the practical limitations of each component.
{% endhint %}

