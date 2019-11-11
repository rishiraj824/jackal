---
title: Using Flux In a Chat Service
---


To embed [SUSI’s API Service](http://api.susi.ai) in a chat-like system, I needed a view which could populate the content dynamically and maintain the state of the Application at the same time. [Flux](https://facebook.github.io/flux/) follows a unidirectional data flow path and I used this feature to the advantage of the Chat Application to maintain the real time state of the Chat View.

A Flowchart model of Flux looks like

![flux flowchart](http://blog.fossasia.org/wp-content/uploads/2017/07/flux-diagram-white-background-750x374.png)

_src:_ [_https://github.com/facebook/flux_](https://github.com/facebook/flux)

Flux uses a **dispatcher service** to render its views, thus making the data flow in a _unidirectional path_. When a user reacts with a React view (here through the TextArea in the chat system), the view propagates an action through the dispatcher service, to the various stores that hold the application’s data and finally update the views that are affected. 

For the current Chat Application, I used a single Message Store which contained all the event listeners to detect any change in the view. For example, when I send a “_Hey”_ to SUSI, an action is called to Dispatch this message to the Message Store with an ActionType ``` “CREATE_MESSAGE”```. This store then renders the message in the Message Section View.

Here is an example snippet from the [Actions.js](https://github.com/fossasia/chat.susi.ai/blob/master/src/actions.js) file which performs an action of type CREATE\_MESSAGE and dispatches the messages to the [MessageStore.js.](https://github.com/fossasia/chat.susi.ai/blob/master/src/stores/MessageStore.js)
```
export function createMessage(text, currentThreadID) {
  let message = ChatMessageUtils.getCreatedMessageData(text, currentThreadID);
  ChatAppDispatcher.dispatch({
    type: ActionTypes.CREATE_MESSAGE,
    message
  });
  ChatWebAPIUtils.createMessage(message);
};
```
The response from the message is generated as soon as another ActionType named

```“CREATE_SUSI_MESSAGE”``` is dispatched to the store, thereby rendering the SUSI’s response generated in the view.

The file [ChatConstants.js](https://github.com/fossasia/chat.susi.ai/blob/master/src/constants/ChatConstants.js) which declares all the ActionTypes.
```
import keyMirror from 'keymirror';

export default {

  ActionTypes: keyMirror({
    CREATE_MESSAGE: null,
    RECEIVE_RAW_CREATED_MESSAGE: null,
    CREATE_SUSI_MESSAGE: null,
    RECEIVE_SUSI_MESSAGE: null,
    RECEIVE_RAW_MESSAGES: null
  })

};
```

To get the message up on the view, I have used the following utils to call the API, render the messages to the view and call the different actions. Here’s a code snippet from [ChatMessageUtils.js](https://github.com/fossasia/chat.susi.ai/blob/master/src/utils/ChatMessageUtils.js)

```
export function createMessage(message) {
  ChatExampleDataServer.postMessage(message, createdMessage => {
    Actions.receiveCreatedMessage(createdMessage, message.id);
  });
  ChatExampleDataServer.postSUSIMessage(message, createdMessage => {
    Actions.createSUSIMessage(createdMessage, message.threadID);
  });
};
```

To know more about the project join us on Gitter at [gitter.im/fossasia/susi\_webchat](http://gitter.im/susi_webchat), or to contribute go to [https://github.com/fossasia/chat.susi.ai/](https://github.com/fossasia/chat.susi.ai/).

A demo application can be found running at [http://chat.susi.ai](http://chat.susi.ai).

### **Resources -**

To know more about Flux you can visit the following websites.

*   Docs and In-Depth Overview [http://facebook.github.io/flux/docs/in-depth-overview.html#content](http://facebook.github.io/flux/docs/in-depth-overview.html#content)
*   Video Tutorials - [http://facebook.github.io/flux/docs/videos.html#content](http://facebook.github.io/flux/docs/videos.html#content)