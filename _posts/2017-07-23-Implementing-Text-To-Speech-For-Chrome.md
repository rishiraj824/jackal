---
titile: Implementing Text to Speech for Chrome.
desc: See how you can add Text to Speech support in your app.
---

[SUSI Web Chat](http://chat.susi.ai) now gives voice replies while chatting with it similar to [SUSI Android](http://github.com/fossasia/susi_android) and [SUSI iOS](http://github.com/fossasia/susi_iOS) clients. To test the Voice Output on Chrome,

1.  Visit [chat.susi.ai](http://chat.susi.ai)
2.  Click on the Mic input button.
3.  Say something using the Mic when the Speak Now View appears

The simplest way to add text-to-speech to your website is by using the official [**Speech API**](https://github.com/mdn/web-speech-api/) currently available for Chrome Browser. The following steps help to achieve it in ReactJS.

1.  Initialize state defaults in a component named, **VoicePlayer.**
```
const defaults = {
      text: '',
      volume: 1,
      rate: 1,
      pitch: 1,
      lang: 'en-US'
    } 
```
There are two states which need to be maintained throughout the component and to be passed as such in our props which will maintain the state. Thus our state is simply
```
this.state = {
      started: false,
      playing: false
    }
```
2.  Our next step is to make use of functions of the Web Speech API to carry out the relevant processes.

1.  **speak()** - window.speechSynthesis.speak(this.speech) - Calls the speak method of the Speech API
2.  **cancel()** - window.speechSynthesis.cancel() - Calls the cancel method of the Speech API

3.  We then use our component helper functions to assign eventListeners and listen to any changes occurring in the background. For this purpose we make use of the functions **componentWillReceiveProps(), shouldComponentUpdate(), componentDidMount(), componentWillUnmount(), and render().**

1.  **componentWillReceiveProps()** - receives the object parameter {pause} to listen to any paused action
2.  **shouldComponentUpdate()** simply returns false if no updates are to be made in the speech outputs.
3.  **componentDidMount()** is the master function which listens to the action start, adds the eventListener start and end, and calls the speak() function if the prop play is true.
4.  **componentWillUnmount()** destroys the speech object and ends the speech.

Here’s a code snippet for Function **componentDidMount()** -
```
componentDidMount () {
  
    const events = [
      { name: 'start', action: this.props.onStart }
    ]
    // Adding event listeners
    events.forEach(e => {
      this.speech.addEventListener(e.name, e.action)
    })

    this.speech.addEventListener('end', () => {
      this.setState({ started: false })
      this.props.onEnd()
    })

    if (this.props.play) {
      this.speak()
    }
  }
```
4.  We then add proper props validation in the following way to our **VoicePlayer component.**
```
VoicePlayer.propTypes = {
  play: PropTypes.bool,
  text: PropTypes.string,
  onStart: PropTypes.func,
  onEnd: PropTypes.func
};
```
5.  The next step is to pass the props from a listener view to the VoicePlayer component. Hence the listener here is the component [MessageListItem.js](https://github.com/fossasia/chat.susi.ai/blob/master/src/components/ChatApp/MessageListItem/MessageListItem.react.js) from where the voice player is initialized.

1.  First step is to initialise the state.
```
this.state = {
      play: false,
    }
  onStart = () => {
    this.setState({ play: true });
  }
  onEnd = () => {
    this.setState({ play: false });
  }
```
2.  Next, we set play to true when we want to pass the props and the text which is to be said and append it to the message lists which have voice set as \`true\`
```
 { this.props.message.voice &&
      (<VoicePlayer
             play
             text={voiceOutput}
             onStart={this.onStart}
             onEnd={this.onEnd}
 />)} 
```
Finally, our message lists with voice true will be heard on the speaker as they have been spoken on the microphone. To get access to the full code, go to the repository [https://github.com/fossasia/chat.susi.ai](https://github.com/fossasia/chat.susi.ai) or on our chat channel at [gitter.im/fossasia/susi\_webchat](http://gitter.im/fossasia/susi_webchat)

Resources
---------

1.  Speak-easy-synthesis repository [http://mdn.github.io/web-speech-api/speak-easy-synthesis](http://mdn.github.io/web-speech-api/speak-easy-synthesis)
2.  Web-speech-api repository [https://github.com/mdn/web-speech-api/](https://github.com/mdn/web-speech-api/)