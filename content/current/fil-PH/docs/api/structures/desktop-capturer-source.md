# DesktopCapturerSource Object

* `id`String - Ang kumikilala sa window o screen na maaaring gamitin bilang isang `chromeMediaSourceId` na tumigil kapag tinatawagan [`navigator.webkitGetUserMedia`]. Ang format ng kumikilala ay ganito `window:XX` o `screen:XX`, na ang `XX` ay bilang na hindi tiyak ang paglabas.
* `name` String - Ang pinagmumulan ng screen ay pinapangalanan na alinman sa `Entire Screen` o `Screen<index>`, samantalang ang pangalan ng pinagmumulan ng window ay tumutugma sa titulo ng window.
* `thumbnail`[NativeImage](../native-image.md) - imahe ng isang thumbnail. **Note:** There is no guarantee that the size of the thumbnail is the same as the `thumbnailSize` specified in the `options` passed to `desktopCapturer.getSources`. Ang aktuwal na sukat ay nakasalalay sa scale ng screen o window.
* `display_id` String - A unique identifier that will correspond to the `id` of the matching [Display](display.md) returned by the [Screen API](../screen.md). On some platforms, this is equivalent to the `XX` portion of the `id` field above and on others it will differ. It will be an empty string if not available.
* `appIcon` [NativeImage](../native-image.md) - An icon image of the application that owns the window or null if the source has a type screen. The size of the icon is not known in advance and depends on what the the application provides.
