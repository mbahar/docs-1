---
---

# XR

The XR category enables you to work with augmented reality (AR) and virtual reality (VR) content within your applications. The XR category has built-in support for Amazon Sumerian.

## Configuration

### Publishing a scene
To download the required scene configuration for your Sumerian scene, visit <a href="https://console.aws.amazon.com/sumerian/home" target="_blank">Amazon Sumerian console</a>, create or open the scene you would like to use with AWS Amplify, click *Publish* dropdown from the top right corner of the Sumerian console, then click *Host privately*:

If your scene was already published publicly, you will need to unpublish then publish again using the instructions below.
{: .callout .callout--info}

![XR]({%if jekyll.environment == 'production'%}{{site.amplify.docs_baseurl}}{%endif%}/js/images/xr/sumerian_host_privately_button.png){: class="screencap" style="max-height:600px;"}


You will then be prompted with the following dialog. Click the *Publish* button:
![XR]({%if jekyll.environment == 'production'%}{{site.amplify.docs_baseurl}}{%endif%}/js/images/xr/sumerian_publish_button.png){: class="screencap" style="max-height:600px;"}

Now click the *Download JSON configuration* button to download the scene configuration JSON that will be used for configuring your scene within AWS Amplify:
![XR]({%if jekyll.environment == 'production'%}{{site.amplify.docs_baseurl}}{%endif%}/js/images/xr/amplify_published_dialog.png){: class="screencap" style="max-height:600px;"}

### Scene authorization

To load Amazon Sumerian scenes you will need to activate the [Amplify Auth category]({%if jekyll.environment == 'production'%}{{site.amplify.docs_baseurl}}{%endif%}/js/authentication).

```
$ amplify add auth
```

Be sure to include an Authenticated Role/Policy with your Cognito Identity Pool. To understand how to configure your Authenticated Role/Policy for Sumerian scene access go to the <a href="https://docs.aws.amazon.com/sumerian/latest/userguide/sumerian-permissions.html" target="_blank">Amazon Sumerian Permissions</a> documentation page. <a href="https://docs.aws.amazon.com/cognito/latest/developerguide/iam-roles.html" target="_blank"> Learn more</a> about IAM Roles with Cognito Identity Pools.
{: .callout .callout--info}

### App configuration

Add the following code to your application to configure the XR category:
```javascript
import Amplify from 'aws-amplify';
import aws_exports from './aws-exports';
import scene1Config from './sumerian_exports_<sceneId>'; // This file will be generated by the Sumerian AWS Console 

Amplify.configure({
  ...aws_exports,
  XR: { // XR category configuration
    region: 'us-west-2', // Sumerian region
    scenes: { 
      "scene1": { // Friendly scene name
        sceneConfig: scene1Config // Scene configuration from Sumerian publish
      }
    }
  }
});
```

You can add optional publish parameters to the scene configuration:
```javascript
XR.configure({ // XR category configuration
  region: 'us-west-2', // Sumerian region
  scenes: { 
    "scene1": { // Friendly scene name
      sceneConfig: scene1Config // Scene configuration from Sumerian publish
      publishParamOverrides: { alpha: true } // Optional publish parameters
    }
  }
});
```

## Scene Usage
The XR Category allows a Sumerian scene to be rendered into an DIV HTML element with `loadScene` method. When the scene has been loaded the *XR.start()* method will start the scene. To render the scene, pass your scene name and the id of the element in the method call:

```javascript
// Load scene with sceneName: "scene1" into dom element id: "sumerian-scene-dom-id"
async loadAndStartScene() {
    await XR.loadScene("scene1", "sumerian-scene-dom-id");
    XR.start("scene1");
}

// HTML
<div id="sumerian-scene-dom-id"></div>
```

Additionally, you can use the [Sumerian Scene UI components]({%if jekyll.environment == 'production'%}{{site.amplify.docs_baseurl}}{%endif%}/js/xr#sumerian-scene) for an out-of-the-box UI solution.

## Scene API

**Using optional progress handlers and options**

To configure the appearance and the behavior of your Sumerian scene, you can use `sceneOptions` parameter in the method call:

```javascript
async loadAndStartScene() {
    const progressCallback = (progress) => {
        console.log(`Sumerian scene load progress: ${progress * 100}%`);
    }

    const sceneOptions = {
        progressCallback
    }
    
    await XR.loadScene("scene1", "sumerian-scene-dom-id", sceneOptions);
    XR.start("scene1");
}
```

### Retrieving the Scene Information

You can check the loading status of the scene with *isSceneLoaded* method. Also, you can use *isMuted* method to retrieve audio information about the loaded scene:

```javascript
if (XR.isSceneLoaded('scene1')) {

    if (XR.isMuted('scene1')) {
        // The scene is muted
        XR.setMuted('scene1', false) // Unmute
    }

}
```

### Entering VR mode

For compatible devices, you can enable VR mode for your scene. When a user enters VR mode with a controller attached, the VR controller component tracks its location in 3D space. 

Entering VR requires user input i.e. button press or similar.
{: .callout .callout--info}

```javascript
if (XR.isSceneLoaded('scene1')) {

    if (XR.isVRCapable('scene1')) {
        XR.enterVR('scene1')
    }

}
```

### Capturing Audio Events

XR Category's scene controller emits audio-related events during scene playback. You can subscribe to those events with `XR.onSceneEvent` and provide audio controls in your app, e.g.: providing a *volume on* button when the browser audio is disabled.

```javascript

XR.onSceneEvent('scene1', 'AudioEnabled', () => console.log ('Audio is enabled') );
XR.onSceneEvent('scene1', 'AudioDisabled', () => console.log ('Audio is disabled') ));

```

### Enabling Audio

In some browsers, playback of audio is disabled until the user provides input. To reliably enable audio in your scene, wait until the user's first input, such as a mouse click or screen touch, and then call the `enableAudio()` method with the scene name.

If the browser is blocking autoplay, the Audio Disabled event will get thrown the first time the scene attempts to PLAY audio, if no user input has been given
{: .callout .callout--info}

```javascript
XR.enableAudio('scene1')
```

### API Reference

For a complete XR reference visit the [API Reference](https://aws-amplify.github.io/amplify-js/api/classes/xr.html)

## UI Components
### Sumerian Scene
After you have [published and configured your Sumerian scene]({%if jekyll.environment == 'production'%}{{site.amplify.docs_baseurl}}{%endif%}/js/xr#configuration) you can use a Sumerian Scene UI component in one of the following supported frameworks

Note: Each of the following UI components will inherit the height and width of the direct parent DOM element. Make sure to set the width and height styling on the parent DOM element to your desired size.
{: .callout .callout--info}

#### React
**Installation**
```
$ npm install aws-amplify-react
```

**Usage**
```javascript
import { SumerianScene } from 'aws-amplify-react';
...

render() {
  return (
    <div className="App">
      // sceneName: the configured friendly scene you would like to load
      <SumerianScene sceneName="scene1" />
    </div>
  );
}
```
See [React configuration]({%if jekyll.environment == 'production'%}{{site.amplify.docs_baseurl}}{%endif%}/js/react#configuration) for additional configuration details.

#### Angular
**Installation**
```
$ npm install aws-amplify-angular
```

**Theme**

In *styles.css*:
```javascript
@import '~aws-amplify-angular/theme.css';
```

**Usage**
```javascript
// sceneName: the configured friendly scene you would like to load
<amplify-sumerian-scene sceneName="scene1"></amplify-sumerian-scene>
```

See [Angular configuration]({%if jekyll.environment == 'production'%}{{site.amplify.docs_baseurl}}{%endif%}/js/angular#configuration) for additional configuration details.

#### Ionic
**Installation**
```
$ npm install aws-amplify-angular
```

**Theme**

In *global.scss*:
```javascript
@import '~aws-amplify-angular/ionic.css';
```
**Usage**
```javascript
// sceneName: the configured friendly scene you would like to load
<amplify-sumerian-scene sceneName="scene1" framework="Ionic"></amplify-sumerian-scene> 
```
See [Ionic configuration]({%if jekyll.environment == 'production'%}{{site.amplify.docs_baseurl}}{%endif%}/js/ionic#configuration) for additional configuration details.

#### Vue
**Installation**
```
$ npm install aws-amplify-vue
```

**Usage**
```javascript
// scene-name: the configured friendly scene you would like to load
<amplify-sumerian-scene scene-name="scene1"></amplify-sumerian-scene> 
```
See [Vue configuration]({%if jekyll.environment == 'production'%}{{site.amplify.docs_baseurl}}{%endif%}/js/vue#configuration) for additional configuration details.
