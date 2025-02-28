# إمكانية الوصول

من المهم جعل التطبيقات سهلة الوصول ويسعدنا تقديم طلبات جديدة وظائف ل Devtron </ 0> و  Spectron </ 1> التي تعطي مطوري الفرصة لجعل تطبيقاتهم أفضل للجميع.</p> 



---

Accessibility concerns in Electron applications are similar to those of websites because they're both ultimately HTML. With Electron apps, however, you can't use the online resources for accessibility audits because your app doesn't have a URL to point the auditor to.

هذه الميزات الجديدة تجلب أدوات التدقيق إلى تطبيق إليكترون. تستطيع اختيار إضافة تدقيقات إلى اختباراتك باستخدام Spectron أو استخدمها في DevTools مع Devtron. تابع القراءة لملخص الأدوات.



## سبيكترون

In the testing framework Spectron, you can now audit each window and `<webview>` tag in your application. For example:



```javascript
app.client.auditAccessibility().then(function (audit) {
  if (audit.failed) {
    console.error(audit.message)
  }
})
```


يمكنك قراءة المزيد حول هذه الميزة في  وثائق Spectron </ 0>.</p> 



## Devtron

In Devtron, there is a new accessibility tab which will allow you to audit a page in your app, sort and filter the results.

![لقطة الشاشة لـdevtron](https://cloud.githubusercontent.com/assets/1305617/17156618/9f9bcd72-533f-11e6-880d-389115f40a2a.png)

Both of these tools are using the [Accessibility Developer Tools](https://github.com/GoogleChrome/accessibility-developer-tools) library built by Google for Chrome. You can learn more about the accessibility audit rules this library uses on that [repository's wiki](https://github.com/GoogleChrome/accessibility-developer-tools/wiki/Audit-Rules).

If you know of other great accessibility tools for Electron, add them to the accessibility documentation with a pull request.



## تمكين الوصول

Electron applications keep accessibility disabled by default for performance reasons but there are multiple ways to enable it.



### داخل التطبيق

By using [`app.setAccessibilitySupportEnabled(enabled)`](../api/app.md#appsetaccessibilitysupportenabledenabled-macos-windows), you can expose accessibility switch to users in the application preferences. User's system assistive utilities have priority over this setting and will override it.



### التكنولوجيات المساعدة

Electron application will enable accessibility automatically when it detects assistive technology (Windows) or VoiceOver (macOS). أنظر Chrome[إمكانية الوصول للوثائق](https://www.chromium.org/developers/design-documents/accessibility#TOC-How-Chrome-detects-the-presence-of-Assistive-Technology)للمزيد من التفاصيل.

On macOS, third-party assistive technology can switch accessibility inside Electron applications by setting the attribute `AXManualAccessibility` programmatically:



```objc
CFStringRef kAXManualAccessibility = CFSTR("AXManualAccessibility");

+ (void)enableAccessibility:(BOOL)enable inElectronApplication:(NSRunningApplication *)app
{
    AXUIElementRef appRef = AXUIElementCreateApplication(app.processIdentifier);
    if (appRef == nil)
        return;

    CFBooleanRef value = enable ? kCFBooleanTrue : kCFBooleanFalse;
    AXUIElementSetAttributeValue(appRef, kAXManualAccessibility, value);
    CFRelease(appRef);
}
```
