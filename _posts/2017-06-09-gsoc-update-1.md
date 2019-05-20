---
title: "GSoC Update #1"
comments: false
---

I started my Google Summer of Code project with the Elm Software Foundation, Elm for iOS. As part of the project, I will be posting weekly status updates. This is the first of those updates.

The updates will be originally posted on the elm-dev email list, and then reproduced here. In order to keep the discussion in one place, please comment on the [elm-dev post](https://groups.google.com/forum/#!topic/elm-dev/mzU6oBCelH4). The following is a reprint of the first update.

> Re: Elm for iOS
>
> Hi all!
>
> I am working on the Elm for iOS project this summer as part of the Google Summer of Code program. The goal of the project is to determine the feasibility of making iOS apps with the Elm programming language. Although there are already projects like elm-native-ui and elm-fuse that allow you to make mobile apps from Elm code, they are non-optimal due to their experimental nature and reliance on third-party code. I hope to build the prototype of a solution that removes this indirection by direct connecting Elm (via some JavaScript) to the iOS APIs. (It is important to note that this does not necessarily mean that you will be able to create iOS apps with Elm by the end of the summer, my goal is just to determine which approaches work best and help create a plan for future development.)
>
> Now, some information about myself. My name is Palmer Paul. I am an undergraduate student at the University of Pennsylvania School of Engineering and Applied Science studying computer science. I have been programming for about 5 years, primarily in Python, JavaScript, OCaml, Java, and most recently, Elm. I have done a fair amount of web development and some computer vision. In school, I learn a lot of theory, like algorithm design/analysis and computability.
>
> I'm going to send an email with updates on the project to the elm-dev listserv roughly every week for the rest of the summer. Without further ado, here's the first update.
>
> I started the project by doing research and planning. I spent a lot of time reviewing the source for React Native in order to learn some of the magic that they use to communicate between JavaScript and Obj-C, as well as how they manage to render the UI using native components when it is defined using JavaScript objects. I explored Facebook's Yoga library, which is a likely candidate for the layout engine for Elm for iOS (Yoga is also used by React Native). I also reviewed the code for elm-lang/virtual-dom with Evan in order to determine what could and could not be re-used for Elm for iOS. (As of now, I think most of the diff and some of the patch code can be re-used, while the render code probably can't.) I also took a glance at and played around with elm-native-ui. I then investigated the process of creating a "native bridge" between iOS and JavaScript by embedding a patched JavaScript runtime into a Cocoa app. I started the  first implementation phase of the project towards the end of the week by beginning to build the native bridge.
>
> I think I now have a good idea of what the project will look like. Here is a rough, high-level outline of my plan (which is, of course, subject to change).
>
> 1. Native bridge written in Swift/Obj-C that uses the JavaScriptCore virtual machine. Exposes some UIKit and YogaKit methods to JavaScript. Eventually, will also allow you to send commands and subscribe to certain device APIs.
> 2. Elm native/kernel functions that let you call the exposed methods above from Elm.
> 3. Modified version of virtual-dom for figuring out what needs to be rendered and rendering it.
>
> I'm excited to be working with Elm this summer. Please don't hesitate to reach out to me with advice or comments about my project or just to say hello. (Especially, if you have experience with JavaScriptCore.) You can usually find me on the elm-lang Slack as palmerpa, or if you prefer email, you can reach me at pzpaul2002@yahoo.com (include Elm in the subject so that I'll know to open it). I'm always willing to chat.
>
> That's all for now. Nice to meet all of you!
>
> Regards,<br>
> Palmer Paul
