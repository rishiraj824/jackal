---
title: Using react-slick in your app for horizontal feeds
---
To populate SUSI RSS Feed generated, while chatting on [SUSI Web Chat](http://chat.susi.ai), I needed a Horizontal Swipeable Tile Slider. For this purpose, I made use of the package [react-slick](https://github.com/akiran/react-slick). The information which was supposed to be handled as obtained from the [SUSI Server](http://api.susi.ai) to populate the RSS feed was

*   Title
*   Description
*   Link

Hence to show all of this information like a horizontal scrollable feed, tiles by react-slick solves the purpose. To achieve the same, let’s see follow the steps below.

1.  First step is to install the [react-slick](https://github.com/akiran/react-slick) package into our project folder, for that we use
```
npm install react-slick --save
```
1.  Next we import the Slider component from react-slick package into the file where we want the slider, here [MessageListItem.react.js](https://github.com/fossasia/chat.susi.ai/blob/master/src/components/ChatApp/MessageListItem.react.js)
```
import Slider from 'react-slick'
```
1.  Add Slider with settings as given in the docs. This is totally customisable. For more customisable options go to [https://github.com/akiran/react-slick](https://github.com/akiran/react-slick)
```
var settings = {
         speed: 500,
         slidesToShow: 3,
         slidesToScroll: 1,
        swipeToSlide:true,
         swipe:true,
         arrows:false
     };
```
speed – The Slider will scroll horizontally with this speed.

slidesToShow – The number of slides to populate in one visible screen

swipeToSlide, swipe – Enable swiping on touch screen devices.

arrows – Put false, to disable arrows

1.  The next step is to initialize the Slider component inside the render function and populate it with the tiles. The full code snippet is available at [MessageListItem.react.js](https://github.com/fossasia/chat.susi.ai/blob/master/src/components/ChatApp/MessageListItem.react.js)
```
<Slider {..settings}>//Append the settings which you created
    {yourListToProps} // Add the list tiles you want to see
</Slider>
```
2.  Adding a little bit of styling, full code available in [ChatApp.css](https://github.com/fossasia/chat.susi.ai/blob/master/src/components/ChatApp/ChatApp.css)
```
 .slick-slide{
 margin: 0 10px;
}
.slick-list{
  max-height: 100px;
}
```

3.  This is the output you would get in your screen.

![](http://blog.fossasia.org/wp-content/uploads/2017/07/Screenshot-from-2017-07-08-13.22.04.png)

*   **Note - To prevent errors like the following on testing with jest, you will have to add the following lines into the code.**

Error log, which one may encounter while using react-slick -
```
 matchMedia not present, legacy browsers require a polyfill

  at Object.MediaQueryDispatch (node_modules/enquire.js/dist/enquire.js:226:19)
  at node_modules/enquire.js/dist/enquire.js:291:9
  at i (node_modules/enquire.js/dist/enquire.js:11:20)
  at Object.<anonymous> (node_modules/enquire.js/dist/enquire.js:21:2)
  at Object.<anonymous> (node_modules/react-responsive-mixin/index.js:2:28)
  at Object.<anonymous> (node_modules/react-slick/lib/slider.js:19:29)
  at Object.<anonymous> (node_modules/react-slick/lib/index.js:3:18)
  at Object.<anonymous> (src/components/Testimonials.jsx:3:45)
  at Object.<anonymous> (src/pages/Index.jsx:7:47)
  at Object.<anonymous> (src/App.jsx:8:40)
  at Object.<anonymous> (src/App.test.jsx:3:38)
  at process._tickCallback (internal/process/next_tick.js:103:7)
```
In [package.json](https://github.com/fossasia/chat.susi.ai/blob/master/package.json), add the following lines-
```
"peerDependencies": {
      "react": "^0.14.0 || ^15.0.1",
      "react-dom": "^0.14.0 || ^15.0.1"
    },
   "jest": {
      "setupFiles": ["./src/setupTests.js", "./src/node_modules/react-scripts/config/polyfills.js"]
   },
```
In [src/setupTests.js](https://github.com/fossasia/chat.susi.ai/blob/master/src/setupTests.js), add the following lines.


```
window.matchMedia = window.matchMedia || (() => { return { matches: false, addListener: () => {}, removeListener: () => {}, }; });
```

**These lines will help resolve any occurring errors while testing with Jest or ESLint.**

To have a look at the full project, visit [https://github.com/fossasia/chat.susi.ai](https://github.com/fossasia/chat.susi.ai) and feel free to contribute. To test the project visit [http://chat.susi.ai](http://chat.susi.ai)

### Resources

*   NPM Package - [React-slick](https://github.com/akiran/react-slick)
*   Official Examples - [http://neostack.com/opensource/react-slick](http://neostack.com/opensource/react-slick)