Evernote SDK for iOS/Mac version 1.2.1
=========================================

What this is
------------
This is a swift version fork for [evernote/evernote-sdk-mac](https://github.com/evernote/evernote-sdk-mac).

Since evernote-sdk-mac for Mac OS is still OC version and cannot mix and match with Swift project, so I give a swift fork for my mac app.

Additional Features
---------------
1. `Define modules` in `Build Settings` set to YES
2. revert mac sdk product name from `EvernoteSDK-Mac` to `EvernoteSDKMac` since `-` cannot appear in umbrella file name
2. add a umbrella file named `EvernoteSDKMac.h`
4. import some header in umbrella file

More information please check [Evernote Github Page](https://github.com/evernote/evernote-sdk-mac/blob/master/README.md)

Issues
----------------
1. I still cannot find why `let noteStore = EvernoteNoteStore.noteStore()` not worked. Even though `EvernoteNoteStore.businessNoteStore()` is work fine. So if you need that method just use define a new one for yourself with follow code:

    let noteStore = EvernoteNoteStore.init(session: EvernoteSession.sharedSession()!)

PS. the `noteStore()` did not appear in the module map file, however I cannot find the reason.



























































































































































































































































