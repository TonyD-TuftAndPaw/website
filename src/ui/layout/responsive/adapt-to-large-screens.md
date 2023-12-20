---
title: Adapt app to large screens
description: Best practices for developing Flutter apps on large device formats
short-title: Adapt to large screens
---

{% include docs/yt_shims.liquid %}

You can optimize  your app to improve its look and feel on large screens. 
Flutter defines [large screens][] as tablets, foldables, and ChromeOS devices 
running Android. Demand for large screens continues to increase. 
As of December 2023, more than [270 million active large screen][] and 
foldable devices run on Android.

## Why does this matter for developers?

When your app supports large screens, they will also receive the
following benefits.

* Increases your business metrics. They would show increased user 
  engagement on tablets for apps with layouts optimized to fill the full screen.
* Improves the [technical quality][] to increase visibility for your
  app in the Play Store on these devices. Recent [Play Store updates][] show 
  ratings by device type and notify users when an app lacks large screen support. 

## Common issues with large screens

This section shares common problems Flutter developers encounter 
and best practices to ensure your Flutter app works well across 
all large screen devices.  

These learnings came from [an audit of the Wonderous app][] against
Android’s large screen guidelines. 

While these issues fall in the context of Android’s large screen devices,
most of these recommendations can also make your app run and feel better on
iPads, desktop browsers, and desktop devices.

### App continuity and configuration changes

#### Requirement

Apps should change orientation and retain or restore [app state][] as the 
device rotates, changes window size or folds and unfolds. 

#### Best practice: Check plugins/native extensions

Your app shouldn’t lose state when the device changes viewable size, 
Flutter should maintain state by default. 

If your app loses state during device configuration, verify that the 
plugins you use support large screen devices. A native extension could 
lose state when the device changes position.

#### Best practice: Use key:PageStorageKey class

To maintain scroll position for lists that keep their layout the same when
device orientation changes, use the key:PageStorageKey class. 
By adding a [PageStorageKey to the collections page][] in the Wonderous app, 
Flutter stores the scroll position of the SingleChildScrollView widget.

While key:PageStorageKey helps maintain scroll position with a list, 
it doesn’t work if your List widget changes layout from vertical to horizontal.  
In this case, you might have to [do a bit of math and change the scroll position on 
screen rotation][].

### Multi-window & split screen

#### Requirement

App can run with full functionality in multi-window mode. 
This could include the app updating its UI or continuing to play media when 
the app doesn’t have top focus.

With the advent of [“screen splitting”][], the user can put the app into less than
two inches of vertical space. In some cases, like Wonderous, the app can get [letterboxed][].
If you use the MediaQuery class to determine the size of the device then disable 
landscape on smaller screens, the app gets letterboxed. 

#### Best practice: Use the Display API

The [MediaQuery][] class doesn’t give you the screen size when you’re in split-screen mode.
It mistakes large devices with split-screens as small devices.

Flutter added a new class to support for [Android’s new window size class][].
By switching to the [Display][] class, Flutter can capture the full screen size and distinguish
a small device from a large device with multiple windows. With this API, you can retrieve various
properties of a display. The new [FlutterView.display][] getter returns a Display object. 
The [Display][] object reports the physical size, the device pixel ratio, and the refresh rate of
the display. To check out an example that uses the new API, check out the [setPreferredOrientations][] method.

When disabling landscape on smaller devices using SystemChrome.setPreferredOrientations(), 
use the Display class to determine the device size rather than the MediaQuery class.

### Keyboard, mouse and trackpad input

#### Requirement

Apps should support basic mice, trackpads, and keyboard shortcuts. 
Main user flows should support keyboard navigation to ensure accessibility. 

Your app must handle navigation and provide accessible Best practices for keyboards on large devices.

#### Best practice: Implement Focus state for custom widgets

Flutter’s Material buttons handle basic focus states, unless you change the default styling of
the Material buttons like Wonderous did to set the overlayColor to transparent. 

Implement a focus state for any custom buttons or gesture detectors in your app. 
If you change the default Material button styles, test for keyboard focus states and 
implement your own if needed. For example, Wonderous [changed the button style][] to have
an outline when the button has focus. To fix it, gSkinner used the FocusNode [hasFocus property][] to
check when the button node had focus input and added a border to the button.

### App UX layout

#### Requirement

Apps should provide adaptive layouts and responsive visual elements that change for 
available screen space or device orientation.

#### Best practice: Use Navigation rail

Verify that your app’s layouts and navigation can function in large screen devices and
~2” of vertical space if the screen works in split-mode. To make this feasible, 
use [adaptive widgets][] like the NavigationRail widget.

### Use camera preview and media projection

#### Requirement

Your app must support camera preview and media projection in 
different orientations, screen sizes, and in multi-window mode.

#### Best practice: Use a JetPack CameraX library

[Showing camera previews][] can be quite complex. You can use the [Jetpack CameraX library][] to
handle many of these details. In Flutter 3.10, the preliminary support was added for 
CameraX to our Flutter camera plugin. To try it, add the following lines to your pubspec.yaml file.

`Dependencies:`
  `camera: ^0.10.4 # Latest camera version`
  `camera_android_camerax: ^0.5.0`

[large screens]: https://developer.android.com/guide/topics/large-screens/get-started-with-large-screens
[270 milion active large screens]: https://developer.android.com/large-screens#:~:text=More%20than%20270%20million%20large%20screen%20Android%20devices
[technical quality]: https://developer.android.com/quality/technical
[Play Store updates]: https://android-developers.googleblog.com/2022/03/helping-users-discover-quality-apps-on.html
[an audit of the Wonderous app]:https://medium.com/flutter/developing-flutter-apps-for-large-screens-53b7b0e17f10
[app state]: https://developer.android.com/jetpack/compose/state#store-state
[PageStorageKey to the collections page]: https://github.com/gskinnerTeam/flutter-wonderous-app/blob/8a29d6709668980340b1b59c3d3588f123edd4d8/lib/ui/screens/wonder_events/widgets/_events_list.dart#L64
[do a bit of math and change the scroll position on 
screen rotation]: https://github.com/gskinnerTeam/flutter-wonderous-app/blob/34e49a08084fbbe69ed67be948ab00ef23819313/lib/ui/screens/collection/widgets/_collection_list.dart#L39
["screen splitting"]: https://developer.android.com/guide/topics/large-screens/multi-window-support
[letterboxed]: https://developer.android.com/guide/topics/large-screens/large-screen-compatibility-mode#letterboxing
[MediaQuery]: https://api.flutter.dev/flutter/widgets/MediaQuery-class.html
[Android’s new window size class]: https://developer.android.com/guide/topics/large-screens/support-different-screen-sizes#window_size_classes
[Display]: https://master-api.flutter.dev/flutter/dart-ui/Display-class.html
[FlutterView.display]: https://master-api.flutter.dev/flutter/dart-ui/FlutterView/display.html
[setPreferredOrientations]: https://master-api.flutter.dev/flutter/services/SystemChrome/setPreferredOrientations.html
[changed the button style]: https://github.com/gskinnerTeam/flutter-wonderous-app/blob/8a29d6709668980340b1b59c3d3588f123edd4d8/lib/ui/common/controls/buttons.dart#L143
[hasFocus property]: https://api.flutter.dev/flutter/widgets/FocusNode/hasFocus.html
[adaptive widgets]: https://docs.flutter.dev/ui/layout/responsive/building-adaptive-apps
[Showing camera previews]: https://developer.android.com/guide/topics/large-screens/large-screen-app-compatibility#camera_preview
[Jetpack CameraX library]: https://developer.android.com/training/camerax