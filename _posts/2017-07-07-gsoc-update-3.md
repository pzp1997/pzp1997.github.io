---
title: "GSoC Update #3"
comments: false
stylesheets:
  - /css/syntax.css
excerpt: <p>Previously on Elm for iOS, I discussed the latest plans for the project and the internals of JavaScriptCore. Since then, I have been implementing those plans by creating a modified version of virtual-dom and a Swift backend for rendering and updating native UIKit elements.</p>
---

> Previously on Elm for iOS, I discussed the latest plans for the project and the internals of JavaScriptCore. Since then, I’ve been working on implementing those plans. I have been approaching the work from two fronts, 1. a modified version of virtual-dom and 2. a Swift backend for rendering and updating native UIKit elements.
>
> ### **VirtualDom**
>
> After a thorough read-through of the virtual-dom code, I started removing the non-reusable parts and the complicated features. The result was a ~700 LOC stripped down version without keyed nodes, thunks, namespaces, the distinction between attributes and properties, events and taggers, and debugging utilities. As the project progresses, I will incrementally add back the relevant features, like thunks and events. The plan is that the diffing and patch creation will continue to happen in JS while the application of the patches will happen on the Swift side.
>
> I also modified the virtual node types to better reflect the proposed API. text and node have been replaced by leaf and parent. The structure of leaf is the same as that of the old node but without fields for children or descendantsCount. It will be used to represent the button, slider, switch, label, and image elements, all of which can have style and layout properties but are childless. parent is equivalent to node sans the descendantsCount field (we’re using a different data structure to represent the patches, which happened to make it obsolete), and for now, it will only be used for the row and column elements. The concepts of attributes, properties, and styles in the original virtual-dom code have been fused into something that most closely resembles properties (and goes by the same name). A new subcategory for Yoga facts has been created in order to make it easier to apply the layout properties in Swift.
>
> As mentioned parenthetically above, the structure of the patches has changed. In the original virtual-dom, each patch was assigned an index according to a DFS preorder traversal and the patches were collectively stored in a list. The idea is that when the real DOM is traversed, those indices can be used to skip unchanged subtrees. This trick can only work, however, if you have access to the entire virtual tree while applying the patches to the real tree. Under our current setup, this is not the case, as only the patches and initial virtual tree are passed to Swift. The solution was to represent the patches using a custom tree data structure, which can give us enough information on the Swift side to efficiently apply the patches. Rather than explain it to you with words, I am just going to show you some Elm types that describe it (note that these types only serve as an approximation, the actual data structure only appears in JS and Swift). You know what they say, a type definition is worth a thousand words!

{% highlight haskell %}
{- PATCHES -}

type alias Patches =
    List PatchTree

type PatchTree
    = Change Patch
    | At Int (List PatchTree)

type Patch
    = Redraw VirtualNode
    | Facts VirtualNode
    | Append (List VirtualNode)
    | RemoveLast Int


{- VIRTUAL NODE -}

type VirtualNode
    = Leaf Tag Facts
    | Parent Tag Facts (List VirtualNode)

type alias Tag =
    String

type alias Facts =
    Dict String (Either String (Dict String String))
{% endhighlight %}

> You can find the [latest version of the modified virtual-dom code here](https://github.com/pzp1997/virtual-dom/blob/master/src/Native/VirtualDom.js).
>
> ### **Swift Backend**
>
> For those acquainted with virtual-dom, you can think of the Swift backend as the initial renderer and patch applier. It exposes two methods to the JSCore context, aptly named initialRender and applyPatches. As of today, the Swift backend supports initial rendering of labels and containers with all UIKit properties, yoga properties for all elements, redraw patches for all elements, and partially facts patches.
>
> The backend itself comes in the form of an app called ElmiOSCore, which will ultimately be some kind of template for creating iOS apps that use Elm. Eventually, there will probably be some CLI tool for creating an Elm iOS app that will generate roughly the same directory structure as ElmiOSCore with the modified virtual-dom and your compiled Elm program included. On a technical level, ElmiOSCore could be described by the following steps.
>
> 1. Initialize/load the app in a stock way
> 2. Create a window object and root view (both required elements for an iOS app)
> 3. Expose the two methods described above to the JSC context
> 4. Load and execute the app.js file (right now this is just a normal JS file that I am using to test things, but in the future, it will be a compiled Elm program)
>
> If you want to play around with the work in progress version of the code, you can [clone it from here](https://github.com/pzp1997/elm-ios-bridge/tree/master/ElmiOSCore). To get started, open the ElmiOSCore.xcworkspace file in Xcode and modify the app.js file. You can make and position different colored labels, go crazy! (Some quick tips for those who want to explore... Make sure that you keep everything, including declarations, inside the body of the ready function. Property names and values should correspond 1:1 with their UIKit versions, so use the Apple docs for those. Yoga properties need to be inside the YOGA sub-fact object. In general, just follow the example that’s there. It is somewhat likely that there are bugs, but don’t bother reporting them for now.)
>
> ### **Future**
>
> On tap for next week is working on the remaining patches and linking the two branches of work described above. After that, I’ll start thinking about events.
>
> As always, you can reach me by commenting on this post, messaging me on the elm-lang Slack @palmerpa, or by sending me an email at pzpaul2002@yahoo.com. I love talking about this stuff, so if you're interested in discussing the project or learning more about anything I mentioned, please drop me a line. Also, thank you to everyone who has already given me feedback.
>
> Best,<br>
> Palmer Paul

This post originally appeared on the elm-dev email list. Please leave all comments there.
