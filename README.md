# Multi-modal chat app

In this project, I developed a chat interface component using React that allows users to interact with an AI system. The component is designed to support both standard text exchanges and image attachments, providing a more flexible chat experience. Here, I’ll walk through the architecture of the component, explaining the logic and reasoning behind each part.

## Overview

The `Chat` component is a functional React component that leverages `useChat` from the AI library (`ai/react`). This library handles core chat functionalities, including message management, input handling, and submission. To enhance the user experience, I designed the interface to display user and AI messages in a structured way, as well as to support image attachments that are displayed within the chat history.

## Initial Setup

I started by importing the necessary hooks and libraries:

```javascript
"use client";
import { useChat } from "ai/react";
import { useState, useRef } from "react";
```

The `useChat` hook provides built-in methods to manage chat interactions (`messages`, `input`, `handleInputChange`, and `handleSubmit`), which reduces boilerplate code and keeps the component lightweight. I also used `useState` to manage file uploads and `useRef` to reference the file input, enabling me to reset it after a file is submitted.

## Structure and Layout

The main structure of the component is built around a flexbox container. This provides a responsive layout, centred within the viewport and sized appropriately for typical chat applications. Here’s how I set up the structure:

```javascript
return (
  <div className="flex flex-col w-full max-w-md py-24 mx-auto stretch">
```

This container wraps all elements of the chat interface, ensuring it remains centred and appropriately spaced on any device.

### Displaying Messages

A critical feature was to display messages clearly and indicate the sender (User or AI). I achieved this by mapping over the `messages` array provided by `useChat`, which stores a history of exchanges. For each message, I dynamically rendered a prefix (`User:` or `AI:`) based on the sender’s role:

```javascript
{
  messages.map((m) => (
    <div key={m.id} className="whitespace-pre-wrap">
      {m.role === "user" ? "User: " : "AI: "}
      {m.content}
    </div>
  ));
}
```

I added conditional logic to filter out and render only image attachments within each message. By checking for `contentType` that starts with `"image/"`, the component is able to display only images, preventing other file types from rendering in this section:

```javascript
<div>
  {m?.experimental_attachments
    ?.filter((attachment) => attachment?.contentType?.startsWith("image/"))
    .map((attachment, index) => (
      <img
        key={`${m.id}-${index}`}
        src={attachment.url}
        width={500}
        alt={attachment.name}
      />
    ))}
</div>
```

### Handling User Input and File Upload

For the user input form, I wanted a persistent interface that remains fixed at the bottom of the component. This enables users to type messages and upload files in one intuitive location.

The form has a few key elements:

1. **File Input**: The file input allows users to upload multiple files, which I handle using `useState`. When files are selected, they are stored in the `files` state variable for inclusion with the next message.

2. **Text Input**: This is where users type their message. It’s tied to the `input` state from `useChat` and updated on every keystroke via `handleInputChange`.

3. **Submit Button**: On form submission, `handleSubmit` sends the message, including any files attached. After submitting, I reset the `files` state to `undefined` and clear the file input using `useRef`:

```javascript
<form
  className="fixed bottom-0 w-full max-w-md p-2 mb-8 border border-gray-300 rounded shadow-xl space-y-2"
  onSubmit={(event) => {
    handleSubmit(event, {
      experimental_attachments: files,
    });

    setFiles(undefined);

    if (fileInputRef.current) {
      fileInputRef.current.value = "";
    }
  }}
>
  <input
    type="file"
    onChange={(event) => {
      if (event.target.files) {
        setFiles(event.target.files);
      }
    }}
    multiple
    ref={fileInputRef}
  />
  <input
    className="w-full p-2 text-black"
    value={input}
    placeholder="Say something..."
    onChange={handleInputChange}
  />
</form>
```

With this setup, the form not only allows file uploads but also resets automatically after submission, ensuring the user can submit multiple messages in succession without any extra steps.

## Conclusion

This `Chat` component provides a seamless and intuitive chat experience with support for both text and image attachments. By utilising `useChat` and React hooks, I streamlined message management and input handling, allowing the focus to remain on user interactions. The component is flexible, responsive, and meets the needs of a modern chat interface with minimal code.
