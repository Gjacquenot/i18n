## Class: DownloadItem

> 控制由遠端來源下載的檔案。

进程: [主进程](../glossary.md#main-process)

`DownloadItem` is an `EventEmitter` that represents a download item in Electron. It is used in `will-download` event of `Session` class, and allows users to control the download item.

```javascript
// 在主處理序裡。
const { BrowserWindow } = require('electron')
let win = new BrowserWindow()
win.webContents.session.on('will-download', (event, item, webContents) => {
  // 設定儲存路徑，不讓 Electron 跳出視窗詢問。
  item.setSavePath('/tmp/save.pdf')

  item.on('updated', (event, state) => {
    if (state === 'interrupted') {
      console.log('下載中斷，無法續傳')
    } else if (state === 'progressing') {
      if (item.isPaused()) {
        console.log('暫停下載')
      } else {
        console.log(`收到的位元組: ${item.getReceivedBytes()}`)
      }
    }
  })
  item.once('done', (event, state) => {
    if (state === 'completed') {
      console.log('下載完成')
    } else {
      console.log(`下載失敗: ${state}`)
    }
  })
})
```

### 物件事件

#### 事件: 'updated'

回傳:

* `event` Event
* `state` String - Can be `progressing` or `interrupted`.

Emitted when the download has been updated and is not done.

The `state` can be one of following:

* `progressing` - The download is in-progress.
* `interrupted` - The download has interrupted and can be resumed.

#### 事件: 'done'

回傳:

* `event` Event
* `state` String - Can be `completed`, `cancelled` or `interrupted`.

Emitted when the download is in a terminal state. This includes a completed download, a cancelled download (via `downloadItem.cancel()`), and interrupted download that can't be resumed.

The `state` can be one of following:

* `completed` - The download completed successfully.
* `cancelled` - The download has been cancelled.
* `interrupted` - The download has interrupted and can not resume.

### 物件方法

The `downloadItem` object has the following methods:

#### `downloadItem.setSavePath(path)`

* `path` String - Set the save file path of the download item.

The API is only available in session's `will-download` callback function. If user doesn't set the save path via the API, Electron will use the original routine to determine the save path(Usually prompts a save dialog).

#### `downloadItem.getSavePath()`

Returns `String` - The save path of the download item. This will be either the path set via `downloadItem.setSavePath(path)` or the path selected from the shown save dialog.

#### `downloadItem.setSaveDialogOptions(options)`

* `options` SaveDialogOptions - Set the save file dialog options. This object has the same properties as the `options` parameter of [`dialog.showSaveDialog()`](dialog.md).

This API allows the user to set custom options for the save dialog that opens for the download item by default. The API is only available in session's `will-download` callback function.

#### `downloadItem.getSaveDialogOptions()`

Returns `SaveDialogOptions` - Returns the object previously set by `downloadItem.setSaveDialogOptions(options)`.

#### `downloadItem.pause()`

Pauses the download.

#### `downloadItem.isPaused()`

Returns `Boolean` - Whether the download is paused.

#### `downloadItem.resume()`

Resumes the download that has been paused.

**Note:** To enable resumable downloads the server you are downloading from must support range requests and provide both `Last-Modified` and `ETag` header values. Otherwise `resume()` will dismiss previously received bytes and restart the download from the beginning.

#### `downloadItem.canResume()`

Returns `Boolean` - Whether the download can resume.

#### `downloadItem.cancel()`

Cancels the download operation.

#### `downloadItem.getURL()`

Returns `String` - The origin url where the item is downloaded from.

#### `downloadItem.getMimeType()`

Returns `String` - The files mime type.

#### `downloadItem.hasUserGesture()`

Returns `Boolean` - Whether the download has user gesture.

#### `downloadItem.getFilename()`

Returns `String` - The file name of the download item.

**Note:** The file name is not always the same as the actual one saved in local disk. If user changes the file name in a prompted download saving dialog, the actual name of saved file will be different.

#### `downloadItem.getTotalBytes()`

Returns `Integer` - The total size in bytes of the download item.

If the size is unknown, it returns 0.

#### `downloadItem.getReceivedBytes()`

Returns `Integer` - The received bytes of the download item.

#### `downloadItem.getContentDisposition()`

Returns `String` - The Content-Disposition field from the response header.

#### `downloadItem.getState()`

Returns `String` - The current state. Can be `progressing`, `completed`, `cancelled` or `interrupted`.

**Note:** The following methods are useful specifically to resume a `cancelled` item when session is restarted.

#### `downloadItem.getURLChain()`

Returns `String[]` - The complete url chain of the item including any redirects.

#### `downloadItem.getLastModifiedTime()`

Returns `String` - Last-Modified header value.

#### `downloadItem.getETag()`

Returns `String` - ETag header value.

#### `downloadItem.getStartTime()`

Returns `Double` - Number of seconds since the UNIX epoch when the download was started.
