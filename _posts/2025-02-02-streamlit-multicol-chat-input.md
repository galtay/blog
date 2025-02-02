---
title: "streamlit app with chat input and multiple columns"
date: 2025-02-02
tags: streamlit
---

An imperfect way to implement a multi-column chat app in streamlit without using CSS.
Check out the live [demo](https://galtay-streamlit-multi-column-chat-app-q6ynqa.streamlit.app/).


[Streamlit](https://streamlit.io/) is an easy way to build web apps in pure Python.
They currently provide a set of [chat elements](https://docs.streamlit.io/develop/api-reference/chat)
including,

* `chat_input` to provide a text input box for users
* `chat_message` to display messages with icons from user and assistant
* `write_stream` to write streaming text output

These components make it easy to build a
[basic LLM chat app](https://docs.streamlit.io/develop/tutorials/chat-and-llm-apps/build-conversational-apps)
that looks quite nice.
You get a chat input field fixed to the bottom of the screen,
support for streaming text,
and a scrolling display of user and assistant messages.
In addition, the messages can contain anything streamlit can render
(e.g. interactive [plotly](https://plotly.com/) graphs).

I wanted to extend this single column layout to support multiple columns.
The idea was to have a chat interface with input and messages in one column
and one or more additional columns that could be used to show more information.
Examples of this kind of pattern are OpenAI's [canvas](https://openai.com/index/introducing-canvas/)
or Anthropic's [artifacts](https://www.anthropic.com/news/artifacts).

The first thing I tried was to simply create two columns and put the existing chat code in one of the columns.

```python
import streamlit as st

left_ratio = 0.5
right_ratio = 1 - left_ratio
left_col, right_col = st.columns([left_ratio, right_ratio])

with left_col:
    # existing chat code with st.chat_input element

with right_col:
    # other stuff to display
```

In this setup, the `chat_input` box was no longer pinned to the bottom of the screen.
Googling around, I found several posts on the streamlit forum from people with similar issues.

* [scrolling-in-a-column/41465](https://discuss.streamlit.io/t/scrolling-in-a-column/41465)
* [how-to-implement-a-three-column-layout-in-chat-input/59466](https://discuss.streamlit.io/t/how-to-implement-a-three-column-layout-in-chat-input/59466)
* [when-using-st-chat-input-inside-st-columns-chat-box-moves-up-how-to-keep-it-stuck-to-the-bottom/61578](https://discuss.streamlit.io/t/when-using-st-chat-input-inside-st-columns-chat-box-moves-up-how-to-keep-it-stuck-to-the-bottom/61578)
* [two-column-chat-with-pdf-interface/66612](https://discuss.streamlit.io/t/two-column-chat-with-pdf-interface/66612)

It seems that streamlit doesn't (yet) have built-in support for floating the chat_input within a container.
There were several solutions involving custom CSS or a package called
[streamlit-float](https://github.com/bouzidanas/streamlit-float).
Those seemed like they could work, but kind of ruined the whole "pure Python" thing.
A simpler but imperfect
[solution](https://discuss.streamlit.io/t/when-using-st-chat-input-inside-st-columns-chat-box-moves-up-how-to-keep-it-stuck-to-the-bottom/61578/3)
was to make the chat messages area a fixed height so that the chat input would always end up below it.

```python
with st.container():
    history = st.container(height=400)
    prompt = st.chat_input("Write something")
```

This is very simple, but means you have to manually specify the height.
I chose this solution as its quick, easy to change, and fine for demos.
To make it a little more flexible I added widgets in the side bar where the user can change the height of the chat area and the size of the columns.


* github repo [here](https://github.com/galtay/streamlit-multi-column-chat)
* live demo [here](https://galtay-streamlit-multi-column-chat-app-q6ynqa.streamlit.app/)












