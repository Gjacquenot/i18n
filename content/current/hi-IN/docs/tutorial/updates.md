# एप्लीकेशन अपडेट

इलेक्ट्रॉन एप्प को अपडेट करने के कई तरीकें हैं | सबसे सरल और आधिकारिक तरीका है अंतर-निर्मित[स्कुइर्रेल](https://github.com/Squirrel) फ्रेमवर्क और इलेक्ट्रान के [ऑटोअपडेटर](../api/auto-updater.md) मोड्यूल का इस्तेमाल करना|

## Using `update.electronjs.org`

The Electron team maintains [update.electronjs.org](https://github.com/electron/update.electronjs.org), a free and open-source webservice that Electron apps can use to self-update. The service is designed for Electron apps that meet the following criteria:

- App runs on macOS or Windows
- App has a public GitHub repository
- Builds are published to GitHub Releases
- Builds are code-signed

The easiest way to use this service is by installing [update-electron-app](https://github.com/electron/update-electron-app), a Node.js module preconfigured for use with update.electronjs.org.

Install the module:

```sh
npm install update-electron-app
```

Invoke the updater from your app's main process file:

```js
require('update-electron-app')()
```

By default, this module will check for updates at app startup, then every ten minutes. When an update is found, it will automatically be downloaded in the background. When the download completes, a dialog is displayed allowing the user to restart the app.

If you need to customize your configuration, you can [pass options to `update-electron-app`](https://github.com/electron/update-electron-app) or [use the update service directly](https://github.com/electron/update.electronjs.org).

## Deploying an Update Server

If you're developing a private Electron application, or if you're not publishing releases to GitHub Releases, it may be necessary to run your own update server.

आपकी आवश्यकताओं के अनुसार, आप निम्नलिखित में से कोई भी चुन सकते हैं:

- [Hazel](https://github.com/zeit/hazel) – Update server for private or open-source apps which can be deployed for free on [Now](https://zeit.co/now). It pulls from [GitHub Releases](https://help.github.com/articles/creating-releases/) and leverages the power of GitHub's CDN.
- [Nuts](https://github.com/GitbookIO/nuts) – Also uses [GitHub Releases](https://help.github.com/articles/creating-releases/), but caches app updates on disk and supports private repositories.
- [electron-release-server](https://github.com/ArekSredzki/electron-release-server) – Provides a dashboard for handling releases and does not require releases to originate on GitHub.
- [Nucleus](https://github.com/atlassian/nucleus) – A complete update server for Electron apps maintained by Atlassian. Supports multiple applications and channels; uses a static file store to minify server cost.

## Implementing Updates in Your App

एक बार जब आपने अपना अपडेट सर्वर स्थापित कर दिया हो, फिर आप आवश्यक मोडयुल्स को अपने कोड में इम्पोर्ट करना शुरू कर सकते हैं | निम्नलिखित कोड विभिन्न सर्वर सॉफ्टवेर के लिए अलग हो सकता है, पर [हेज़ल](https://github.com/zeit/hazel) का इस्तेमाल करने के दौरान यह नीचे दिए गये विवरण की तरह काम करता है |

**महत्वपूर्ण:** कृप्या ध्यान दें कि निम्नलिखित कोड केवल आपकी पैकेज्ड एप्प में चलेगा, न कि विकास में | वातावरण को जाँचने के लिए आप [electron-is-dev](https://github.com/sindresorhus/electron-is-dev) का इस्तेमाल कर सकते हैं |

```javascript
const { app, autoUpdater, dialog } = require('electron')
```

उसके बाद, अपडेट सर्वर का यूआरएल निर्मित करें और [ऑटोअपडेटर](../api/auto-updater.md) को उसके बारे में बतायें:

```javascript
const server = 'https://your-deployment-url.com'
const url = `${server}/update/${process.platform}/${app.getVersion()}`

autoUpdater.setFeedURL({ url })
```

As the final step, check for updates. The example below will check every minute:

```javascript
setInterval(() => {
  autoUpdater.checkForUpdates()
}, 60000)
```

Once your application is [packaged](../tutorial/application-distribution.md), it will receive an update for each new [GitHub Release](https://help.github.com/articles/creating-releases/) that you publish.

## Applying Updates

अब जब आपने अपनी एप्लीकेशन के लिए बुनियादी अपडेट प्रणाली को कॉन्फ़िगर कर लिया है, तो आपको यह सुनिश्चित करना होगा कि जब भी एक अपडेट आये तो उपयोगकर्ता को उसके बारे में सुचना मिलें | इसे आप ऑटोअपडेटर ऐपीआई [इवेंट्स](../api/auto-updater.md#events) का इस्तेमाल कर के प्राप्त कर सकते हैं:

```javascript
autoUpdater.on('update-downloaded', (event, releaseNotes,
releaseName) => {
  const dialogOpts = {
    type: 'info',
    buttons: ['Restart', 'Later'],
    title: 'Application Update',
    message: process.platform === 'win32' ? releaseNotes : releaseName,
    detail: 'A new version has been downloaded. Restart the application to apply the updates.'
  }

  dialog.showMessageBox(dialogOpts).then((returnValue) => {
    if (returnValue.response === 0) autoUpdater.quitAndInstall()
  })
})
```

Also make sure that errors are [being handled](../api/auto-updater.md#event-error). Here's an example for logging them to `stderr`:

```javascript
autoUpdater.on('error', message => {
  console.error('There was a problem updating the application')
  console.error(message)
})
```

## Handing Updates Manually

Because the requests made by Auto Update aren't under your direct control, you may find situations that are difficult to handle (such as if the update server is behind authentication). The `url` field does support files, which means that with some effort, you can sidestep the server-communication aspect of the process. [Here's an example of how this could work](https://github.com/electron/electron/issues/5020#issuecomment-477636990).
