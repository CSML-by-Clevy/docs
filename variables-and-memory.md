# Variables and Memory

CSML has two types of variables: local variables, which have a very short lifecycle, and more global variables, which are called `memories`.

Local variables are only usable within the current step. It is rather a way to write more readable, expressive code. But they are really powerful, because they allow you to do everything that a regular memory does, but temporarily. For more information about local variables, see the `use..as` keyword.

Memories on the other hand are at the core of the purpose of CSML. When two persons converse, they constantly memorize information, which they can reuse in other discussions. For example, if I gathered that my friend likes Iron Man, I might propose later that we go see Captain America together. I do not have to ask them again about their favorite film, because I already learned this information before, even if it was in a different conversation, even if the topic of that conversation might have been completely unrelated.

The `memory` API is very powerful, and by default a bot never forgets anything about the current user. For more information, see the `remember` keyword.

There is a third memory type: `metadata` \(which can be compared to the user's context at the beginning of the conversation\). This memory type is injected by the channel at the beginning of the conversation and contains any data that the channel already has about the user that can be used in the conversation later. For example, this could include the user's full name or email address, their job title, where they are from...

You can convert a local variable to a long-term memory by overwriting with a \`remember\` it at any time.

## Global variables

Inside any given step, a number of global variables are made available to the developer. These variables include past memories \(`remember`\), step context \(`use`\), and input metadata.

* `event` \(Event\): only defined in the step triggered immediately after a user's input. It contains the user input, or `NULL` when empty
* `retries` \(Number\): number of times the current step has been reached previously in the current conversation
* `_metadata` \(Memory\): read-only memory object that is injected into the conversation by the channel. Usage: `_metadata.something`

## ⚠️ Limitations

Variables and memories \(of any type\) can **not be larger than 200KB**. Using data larger than 200KB in variables \(for example as the return value of a function\) may result in undefined behavior. Also, please note that messages larger than 30KB in size can not be displayed in the test webapp.

In general, you

## 
