# IpcRendererEvent Object extends `Event`

* `sender` IpcRenderer - The `IpcRenderer` instance that emitted the event originally
* `senderId` Integer - The `webContents.id` that sent the message, you can call `event.sender.sendTo(event.senderId, ...)` to reply to the message, see [ipcRenderer.sendTo](#ipcrenderersendtowindowid-channel--arg1-arg2-) for more information. This only applies to messages sent from a different renderer. Messages sent directly from the main process set `event.senderId` to `0`.
