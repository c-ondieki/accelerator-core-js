![logo](./tokbox-logo.png)

# OpenTok Accelerator Core<br/>Version 0.0.1


```javascript
otCore.init(options) =>
	Multi-Party Audio/Video +
	Text Chat +
	Screen Sharing +
	Annotation +
	Archiving
```

OpenTok Accelerator Core provides a simple way to integrate real-time audio/video into your web application using the OpenTok Platform.  `Core` also integrates with and manages the following accelerator packs:

 - [Text Chat](https://www.npmjs.com/package/opentok-text-chat)
 - [Screen Sharing](https://www.npmjs.com/package/opentok-screen-sharing)
 - [Annotation](https://www.npmjs.com/package/opentok-annotation)
 - [Archiving](https://www.npmjs.com/package/opentok-archiving)

Sample Applications
-------------------
There are two sample applications for `Core` .  The [React](https://github.com/opentok/accelerator-core/tree/develop/js/react-sample-app) sample application was built with [Create React App](https://github.com/facebookincubator/create-react-app) and uses [webpack](https://webpack.github.io/) to transpile code.  The other [sample application](https://github.com/opentok/accelerator-core/tree/develop/js/vanilla-js-sample-app) is built with vanilla JavaScript.

Installation
------------

```bash
npm i --save opentok-acc-core
```
webpack/Browserify:
```javascript
const otCore = require('opentok-acc-core');
```
Browser:
```javascript
<script src="path/to/browser/opentok-acc-core.js"></script>
```
Installation

Options
-------
`Core` can be configured in a number of ways, but the only required options property is `credentials`.

The `packages` property specifies which accelerator packs should be included in the application.  If using a bundler like [`webpack`](https://webpack.github.io/) or [`Browserify`](http://browserify.org/), you'll need to install the additional packages using `npm`.  Otherwise `Core` will look for them in global scope.

The `containers` property specifies the DOM elements to be used as containers for video streams.  The remainder of the options properties are specific to individual accelerator packs.

```javascript
const options = {
  credentials: {
    apiKey: yourOpenTokApiKey,
    sessionId: yourOpenTokSessionId,
    token: yourOpenTokToken,
  },
  packages: ['textChat', 'screenSharing', 'annotation', 'archiving'],
  /* A container can either be a query selector or an HTMLElement */
  containers: {
	/* Default for publishers is '#publisherContainer' */
    publisher: {
      camera: '#cameraPublisherContainer',
      screen: '#screenPublisherContainer',
    },
   	/* Default for subscribers is '#subscriberContainer' */
    subscriber: {
      camera: '#cameraSubscriberContainer',
      screen: '#screenSubscriberContainer',
    },
    /* Default controls container is '#videoControls' */
    controls: '#controls',
    /* Default text chat container is '#chat' */
    chat: '#chatContainer',
  },
  communication: {
    callProperites: myCallProperties,
  },
  textChat: {
    name: currentUser.name,
    waitingMessage: 'Messages will be delivered when other users arrive',
  },
  screenSharing: {
    extensionID: 'yourChromeExtensionId',
    extensionPathFF: 'yourFireFoxExtensionURL',
    annotation: true,
    /* Must be 'true' if sharing the current browser window */
    externalWindow: false,
    /* Allow screen sharing over http in development(localhost) */
    dev: true,
    screenProperties: null,
  },
  annotation: {
    items: myToolbarItems,
    colors: myColorPalette,
    onScreenCapture: myScreenCaptureCallback,
  },
  archiving: {
    startURL: 'https://yourapi.com/startArchive',
    stopURL: 'https://yourapi.com/stopArchive',
  }
};

```

Usage
--------------
Initialize `Core`:
```javascript
otCore.init(options);
```
Connect to the session:
```javascript
otCore.connect().then(() => this.setState({ connected: true }));
```

To keep your UI in sync, `Core` exposes a number of events which can be accessed using the `on` method:
```javascript
otCore.on('streamCreated', callback);
```
The data passed to the callback for the following events . . .
```javascript
const events = [
    'subscribeToCamera',
    'unsubscribeFromCamera',
    'subscribeToScreen',
    'unsubscribeFromScreen',
    'startScreenShare',
    'endScreenShare',
];
```
will always include the current `publishers`, `subscribers`, and a `meta` object which provides a count of the current `publishers` and `subscribers`, making it easy to keep your UI in sync.

```javascript
  meta: {
    publishers: {
      camera: 1,
      screen: 0,
      total: 1,
    },
    subscribers: {
      camera: 2,
      screen: 1,
      total: 3,
    },
  },
  publishers: {
    camera: {
      'OT_d18d5027-21eb-093f-8c18-e3959f3e7585': OTPublisherObject,
    },
    screen: {}
  },
  subscribers: {
    camera: {
      'OT_d18nd82s-21eb-4b3f-82n8-e3nd723e7585': OTSubscriberObject,
      'OT_d38n82s-9n2b-4sdf-82n8-eadnfdf92nf90': OTSubscriberObject,
    },
    screen: {
      'OT_nd872bd9s-0d82-n431-809l-k1kdjd72mdks': OTSubscriberObject,
    },
  }
```
The full list of events can be seen [here](https://github.com/opentok/accelerator-core/blob/develop/js/src/events.js).