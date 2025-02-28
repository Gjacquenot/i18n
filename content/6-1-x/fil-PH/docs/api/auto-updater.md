# awtoUpdeyter

> Paganahin ang app na awtomatikong mag-update ang kanilang sarili.

Proseso:[Pangunahi](../glossary.md#main-process)

**See also: [A detailed guide about how to implement updates in your application](../tutorial/updates.md).**

## Platform Notices

Currently, only macOS and Windows are supported. There is no built-in support for auto-updater on Linux, so it is recommended to use the distribution's package manager to update your app.

In addition, there are some subtle differences on each platform:

### macOS

Sa macOS, ang `autoUpdater` na modyul ay ginawa [sa Squirrel.Mac](https://github.com/Squirrel/Squirrel.Mac), ibig sabihin hindi mo kailangan ng kahit anong espesyal na set-up para mapagana ito. Para sa mga kinakailangan ng panig ng server, maari mong basahin ang [Server Support](https://github.com/Squirrel/Squirrel.Mac#server-support). Tandaan lamang na ang [App Transport Security](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35)(ATS) ay nalalapat sa lahat ng mga kahilingan na ginawa bilang bahagi ng proseso ng pag-update. Ang mga apps na kailangan hindi isama sa ATS ay maaaring idadag sa susi ng `NSAllowsArbitraryLoads` para sa kanilang app plist.

**Note:** Your application must be signed for automatic updates on macOS. This is a requirement of `Squirrel.Mac`.

### Windows

Sa Windows, ay kailangan mong ilagay ang iyong app sa makina ng gagamit bago mo magamamit ang `autoUpdater`, kaya't inirerekomenda na iyong gamitin ang [electron-winstaller](https://github.com/electron/windows-installer), [electron-forge](https://github.com/electron-userland/electron-forge) or the [grunt-electron-installer](https://github.com/electron/grunt-electron-installer) na pakete para mabuo ang Windows installer.

Kapag ginagamit ang [electron-winstaller](https://github.com/electron/windows-installer) o ang [electron-forge](https://github.com/electron-userland/electron-forge) siguraduhing hindi mo subukan mag-update ng iyong app[ sa unang pagkakataon na ito ay tumatakbo](https://github.com/electron/windows-installer#handling-squirrel-events)(Mangyari ring tignan [ang isyung ito para sa karagdagang impormasyon](https://github.com/electron/electron/issues/7155)). Inirerekomenda rin naming gumamit ng [electron-squirrel-startup](https://github.com/mongodb-js/electron-squirrel-startup)para makakuha ng shortcut sa desktop para sa iyong app.

Ang installer na nabuo gamit ang Squirrel ay lilikha ng isang shortcut icon na mayroong[Application User Model ID](https://msdn.microsoft.com/en-us/library/windows/desktop/dd378459(v=vs.85).aspx) sa format ng `com.squirrel.PACKAGE_ID.YOUR_EXE_WITHOUT_DOT_EXE`,halimbawa nito ay `com.squirrel.slack.Slack` at `com.squirrel.code.Code`. Kailangan mong gamitin ang parehong ID para sa iyong app sa `app.setAppUserModelId` API, kung hindi ang Windows ay hindi magagawang ilagay ang iyong app ng wasto sa iyong task bar.

Hindi gaya ng sa Squirrel.Mac, ang Windows ay kayang mag-host ng update sa S3 o sa kahit anong static file ng host. Maari mong basahin ang dokumento ng [Squirrel.Windows](https://github.com/Squirrel/Squirrel.Windows)para makakuha pa ng higat pang detalye tungkol sa kung paano gumagana ang Squirrel.Windows.

## Pangyayari

Ang `autoUpdater` maglalabas ng mga ganitong pangyayari:

### Pangyayari: 'error'

Ibinabalik ang:

* `error` Error

Lumabas kapag mayroong mali habang ina-update.

### Pangyayari:'checking-for-update'

Lumalabas kapag sinusuri kung ang update ay nagsimula na.

### Pangyayari: 'update-available'

Emitted when there is an available update. The update is downloaded automatically.

### Pangyayari: 'update-not-available'

Napalabas kapag walang available na pag-update.

### Pangyayari: 'update-downloaded'

Ibinabalik ang:

* `event` na Kaganapan
* `releaseNotes` Lupid
* `releaseNotes` Lubid
* `releaseDate` Petsa
* `updateURL` Lubid

Lumalabas kung ang update ay nadownload na.

Tanging Windows lamang`releaseName` is available.

**Note:** It is not strictly necessary to handle this event. A successfully downloaded update will still be applied the next time the application starts.

### Event: 'before-quit-for-update'

This event is emitted after a user calls `quitAndInstall()`.

When this API is called, the `before-quit` event is not emitted before all windows are closed. As a result you should listen to this event if you wish to perform actions before the windows are closed while a process is quitting, as well as listening to `before-quit`.

## Pamamaraan

Ang `autoUpdater` na gamit ay mayroong ibat-ibang pamamaraan:

### `autoUpdater.setFeedURL(options)`

* `options` Object
  * `url` Tali
  * `headers` Object (optional) _macOS_ - HTTP request headers.
  * `serverType` String (optional) _macOS_ - Either `json` or `default`, see the [Squirrel.Mac](https://github.com/Squirrel/Squirrel.Mac) README for more information.

Tinatakda ang `url` at nagpapasimula ng auto-updater.

### `autoUpdater.getFeedURL()`

Bumalik `String` - Ang kasalukuyang update feed URL.

### `autoUpdater.checkForUpdates()`

Asks the server whether there is an update. You must call `setFeedURL` before using this API.

### `autoUpdater.quitAndInstall()`

Restarts the app and installs the update after it has been downloaded. It should only be called after `update-downloaded` has been emitted.

Under the hood calling `autoUpdater.quitAndInstall()` will close all application windows first, and automatically call `app.quit()` after all windows have been closed.

**Note:** It is not strictly necessary to call this function to apply an update, as a successfully downloaded update will always be applied the next time the application starts.
