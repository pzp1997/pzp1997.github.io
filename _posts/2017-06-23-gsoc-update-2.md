---
title: "GSoC Update #2"
comments: false
---


The planning stage of the project is nearly done. After some trial and error, we have discovered a promising approach for how to construct the Elm-Swift bridge.

Update #2 for the Elm for iOS project comin' right atcha!

#### Overview

Since my previous update, I...

1. Made a simple app in Swift with Yoga and began porting it to JavaScript.
2. Started bridging UIKit using JavaScriptCore (JSC) in pursuit of #1.
3. Came up with a modified approach to the project with Evan partly in response to the difficulty of #2.
4. Explored the internals of JSC and React Native (RN) to figure out exactly how values cross the bridge.

For those who are interested, here are the details (in order of future importance).

#### The Latest Approach

The refined approach is as follows.

When the app is launched, spawn a secondary thread for executing Elm / JS code. This thread will run your compiled Elm application using a modified version of VirtualDom (we'll call it vdom&prime; for now). vdom&prime; will produce a list of patches that need to be applied to the UI. Using JSC, we will send the list of patches back to the main thread and convert it to an Obj-C/Swift list/array. On the main thread which is running Obj-C/Swift code, we will receive these patches and apply them using the provided UIKit methods. This satisfies Apple's criteria that "Manipulations to your application's user interface must occur on the main thread."

In my discussions with Evan, we also settled upon a first draft of the API.

{% gist 35f731ba8f3163e4611ed991b5b62918 %}

This should look pretty familiar, as we chose to model it in a similar way to the Html module. One key difference is that the primitive elements cannot have any children (think of them as the atoms you can use to build the entire of the UI). The idea behind the attributes is that any conceivable layout or style property for an element would be of type Attribute msg. I will note that not all properties can be applied to all elements. It does not make much sense for an image to have a fontSize, for example. For the first version, we are choosing to silently ignore these incompatibilities. Evan and I created a [spreadsheet](https://docs.google.com/spreadsheets/d/13dQbhNPhkmafu2uyi-tXc8x_09wIz2QYkxcfQ_0rmps/edit#gid=0) with all the properties that can be sensibly applied to a given element and some additional information about the elements. We are of course planning on adding more elements once we can get the ones above working.

The next step is to begin modifying VirtualDom. This begins with designing an internal representation for the nodes and figuring out how to best serialize the patches. Any and all advice on this front is welcome.

#### Internals of JSC and RN

(The following section assumes a basic knowledge of JSC. If you are looking for an overview, this [article](https://nshipster.com/javascriptcore/) can catch you up to speed.)

JSC provides the ability to marshal values between Obj-C and JS. You can send any serializable value over the bridge. From my understanding, the way it works is by encoding your value/data as a JSON string and decoding and processing it on the other end (the process may be slightly different for primitives and functions). Here are some of the relevant [method headers](https://github.com/WebKit/webkit/blob/5277f6fb92b0c03958265d24a7692142f7bdeaf8/Source/JavaScriptCore/API/JSValueRef.h#L272-L290) for the JSON serialization from the source code for JSC. This process is not cheap, so we want to use it as sparingly as possible.

Another important distinction to make is that JSC actually comes in two varieties, a C API and an Obj-C API. The Obj-C API uses the C API under the hood but is nicer to work with for a number of reasons. RN almost exclusively uses the C API, perhaps for performance reasons or in order to modularize the bridging process to make it easier for developers to extend RN with custom Obj-C components (these are just my conjectures, so if anyone knows the definitive reason please let me know). For the first iteration of the Elm for iOS project, I am going to try to use the Obj-C API as much as possible. If we later find that we need to optimize part of the native bridge, we can explore the benefits of using the C API then.

#### Counter App

The app is a basic counter, with a UILabel for displaying the current count and UIButtons for incrementing and decrementing the count. I used Yoga as the layout engine, which was very easy to use (I mean it's basically just flexbox!). If you want to check it out, here is the [source code](https://github.com/pzp1997/elm-ios-bridge/tree/master/CounterSwift).

I started porting the app to use mostly JavaScript. The process I used to do this was as follows. I bridged the classes from UIKit that I was using (UIView, UILabel, UIButton, etc.) by extending them to conform to the JSExport protocol. The JSExport protocol is provided by JSC and basically, serves as a way of specifying what parts of a class to expose in the JavaScript global context. One of the downsides of JSExport for our purposes is that we need to manually specify which variables and methods to export for each class in UIKit and whatever other frameworks we choose to support in the future. (And apparently, it is [quite hard to circumvent this requirement](https://brandonevans.ca/post/text/dynamically-exporting-objective-c-classes-to/) at runtime using introspection and the like.) Apple provides a lot of classes and functionality, and the APIs tend to change every year when a new iOS version is introduced. Therefore, the "extend all classes with JSExport" approach does not scale well and is more or less untenable from our perspective

I stopped working on this version of the app once we realized the flaws in the approach, but if you are curious as to what it looks like, feel free to look at the [source code](https://github.com/pzp1997/elm-ios-bridge/tree/master/CounterElm). Most of the classes used in the app are exported to JS. I was having a hard time adding actions (iOS version of an event listener) to the buttons in JS, so I ended up only making the label and managing the model on the JS side.

That's all for now. As always, you can reach me by commenting on this post, messaging me on the elm-lang Slack @palmerpa, or by sending me an email at pzpaul2002@yahoo.com.

Regards,  
Palmer Paul

_This post originally appeared on the elm-dev email list. Please leave all comments there._
