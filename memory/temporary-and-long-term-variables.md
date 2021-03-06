# Temporary and Long-Term variables

## Temporary Variables

**Local or temporary variables** are only usable within the current step. It is rather a way to write more readable, expressive code. But they are really powerful, because they allow you to do everything that a regular memory does, but temporarily. For more information about local variables, see the `as` keyword.

Local variables are useful for temporary calculations. You do not want to memorize your internal temporary steps, rather save useful information about the user.

```cpp
somestep:
  do tmpvar = "Hi there"
  say tmpvar // "Hi there"
  goto otherstep
  
otherstep:
  say tmpvar // NULL
```

## Long-Term Memory

**Long-term memories** on the other hand are at the core of the purpose of CSML. When two persons converse, they constantly memorize information, which they can reuse in other discussions. For example, if I gathered that my friend likes Iron Man, I might propose later that we go see Captain America together. I do not have to ask them again about their favorite film, because I already learned this information before, even if it was in a different conversation, even if the topic of that conversation might have been completely unrelated.

The memory API is very powerful: by default a bot never forgets anything about the current user. For more information, see the `remember` keyword.

```cpp
somestep:
  remember tmpvar = "Hi there"
  say tmpvar // "Hi there"
  goto otherstep
  
otherstep:
  say tmpvar // "Hi there"
```

## The Memory object

CSML provides a `_memory` global, read-only variable, which contains all variables saved until now for the current user. This  especially useful if you need to debug the state of a user's memory at any given step:

```cpp
somestep:
  remember something = 1
  // ...

someotherstep:
  do something = 8
  // ...
  
wherever:
  // when we get there, is `something` set to 1, 8
  // or even available at all?
  say "{{_memory}}"
  
  
```

## ⚠️ Limitations

{% hint style="warning" %}
Variables and memories \(of any type\) can **not be larger than 50KB**. Using data larger than 50KB in variables \(for example as the return value of a function\) may result in undefined behavior. Also, please note that messages larger than 30KB in size can not be displayed in the test webapp.
{% endhint %}

