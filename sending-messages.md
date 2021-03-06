# Sending Messages

CSML is able to handle many types of messages by default, and can be extended to accommodate any number of custom types as well \(feature documentation pending\).

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

> _\* the content of the parameter may be modified to accommodate the target channel requirements_

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

