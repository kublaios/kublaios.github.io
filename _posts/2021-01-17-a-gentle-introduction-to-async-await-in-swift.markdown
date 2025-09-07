---
id: 88
title: 'A gentle introduction to async/await in Swift'
date: '2021-01-17T21:45:04+00:00'
author: 'Kubilay Erdogan'
layout: post
guid: 'https://www.kubilayerdogan.net/?p=88'
permalink: /a-gentle-introduction-to-async-await-in-swift/
categories:
    - 'Blog Posts'
tags:
    - Concurrency
    - Swift
---

![Olympics Running](/assets/img/posts/2021/swift-async-await/olympics-running-1024x576.jpg)
*Ian Walton/Getty Images*Swift was announced at WWDC 2014, and at the end of 2015, it was made open-source under the Apache License 2.0 (with some exceptions). If you have been following along since the beginning, the language had so many changes, especially on the surface (syntax), that after some period of time they almost became a developer joke, like whether the changes were to rename methodX to aMethodX in the next release (credits to good ol' Xcode who would do the most of the migration automatically).

This is, of course, also due to the nature of the open-source software. The exception with Swift is, in my opinion, that it now became a software language that's (well, to a certain degree) community-driven, but also has the resources to continue its evolution, having a tech titan behind. It's hard to argue with the assessment that this is an amazing thing for the community and the software itself.

One of the things that I like about the open-source software development is the transparency. You are able opt-in all the events in the life of a feature even from the very beginning. Which means, if you really want to, you can even be a part of the change yourself. If not, you can just keep monitoring and get excited about it until it's released, and who knows, maybe you will already be a master of the feature by the time it's released!

If you are here before async/await is released or announced very publicly (I guess it means to get promoted during WWDC, etc.), then you are probably in the second group I mentioned above. After raising awareness regarding what a great thing open-source software is, we can finally talk about it.

## Why?

There's [this proposal](https://github.com/apple/swift-evolution/blob/main/proposals/0296-async-await.md) that's made for the feature and I'll try to summarize it below, leaving some comments. It makes sense to start with the "why" which is explained very well in the "Motivation" section of the proposal, roughly as follows:

### Problem 1: Pyramid of doom

> The pyramid of doom is a common problem that arises when a program uses many levels of nested indentation to control access to a function.
> 
> <cite>[Wikipedia](https://en.wikipedia.org/wiki/Pyramid_of_doom_(programming))</cite>

Closures. As soon as we learned how to use them, we started building these pyramids ourselves, embedding one closure in another. Apparently, over time it became so disturbing for everyone that it is mentioned as the first reason for this change.

### Problem 2: Error handling

> Callbacks make error handling difficult and very verbose. (…) The addition of Result to the standard library improved on error handling for Swift APIs. Asynchronous APIs were one of the main motivators for Result. It's easier to handle errors when using Result, but the closure-nesting problem remains.

### Problem 3: Conditional execution is hard and error-prone

If you really wonder what that really means, see [here](http://Problem%203:%20Conditional%20execution%20is%20hard%20and%20error-prone), but basically, the problem is:

> This pattern inverts the natural top-down organization of the function: the code that will execute in the second half of the function must appear *before* the part that executes in the first half.

Even though I agree with the problem, the example that was given in the link doesn't seem like the perfect fit, I mean, why don't you just create a new method rather than creating a closure and assign it to a constant? But anyway, this doesn't change the fact that this problem does exist when you can't just export the code into a method.

### Problem 4: Many mistakes are easy to make

> It's quite easy to bail-out of the asynchronous operation early by simply returning without calling the correct completion-handler block.

It simply means that you have something to do as soon as your closure is about to exit, but if you have lots of conditions inside of your closure and in some of them you exit the closure earlier, it's easy to forget your last step code before exiting the closure. This happened to me many times, I'm sure it happened to you as well.

### Problem 5: Because completion handlers are awkward, too many APIs are defined synchronously

Seriously? That's just... Sad.

I think everything above is somewhat reasonable, but the ones that I agree with the most are #1 and #4.

## Solution in a nutshell

> Asynchronous functions—often known as async/await—allow asynchronous code to be written as if it were straight-line, synchronous code.

So, for example, this:

```
func printDelayedVar() {
    self.getMyDelayedVar { (delayedVar) in
        print(delayedVar)
    }
}
​
func getMyDelayedVar(completion: (Int) -> Void) {
    DispatchQueue.main.async {
        sleep(3)
        completion(5)
    }
}
```

Becomes this:

```
@asnycHandler func printDelayedVar() {
    let delayedVar = await self.getMyDelayedVar()
    print(delayedVar)
}
​
func getMyDelayedVar() async -> Int {
    // Code to be revealed in the rest of the article
}
```

No reason to worry because of your pyramids getting lost. We still neste things within one another inside of the `async` methods, but at least we call our `async` method like a synchronous method in `printDelayedVar`. I guess the pyramids are just getting... Smaller?

Before moving further, I want to make a disclaimer that the part that **I'm covering in this post is simply the tip of the iceberg**. In the [announcement post](https://forums.swift.org/t/accepted-with-modification-se-0296-async-await/43318) of the approval of this proposal, the review manager says:

> This is an important first step on our concurrency journey. To help navigate how this proposal relates to expected future proposals, this diagram should be of help:

![Swift Concurrency Dependencies](/assets/img/posts/2021/swift-async-await/SwiftConcurrencyDependencies-1024x265.jpg)
*Swift Concurrency Dependencies*As you can see, concurrency in Swift is in fact just getting warmed up. If you want to learn more about this feature, see the References section at the end of the page.

## async/await hands on

Before experimenting with this syntax, there are a couple of things we need to do:

##### 1. Download and install the latest\* development snapshot from swift.org.

Go to [swift.org/download/#snapshots](https://swift.org/download/#snapshots) and from the section 'Trunk Development (main)', download and install the one that's for Xcode.

\* Some things might have changed until the time you read this post, so try [this alternative snapshot](https://swift.org/builds/development/xcode/swift-DEVELOPMENT-SNAPSHOT-2021-01-14-a/swift-DEVELOPMENT-SNAPSHOT-2021-01-14-a-osx.pkg) if things work out differently for you after you install.

##### 2. Configure Xcode to use the respective toolchain

Open Xcode and from the `Xcode` menu, select `Toolchains` -&gt; `Swift Development Snapshot YYYY-mm-dd`. You can do the same through `Preferences` -&gt; `Components` -&gt; `Toolchains`.

##### 3. Add flags to enable experimental feature

From your Project's build settings, find `Swift Compiler - Custom Flags` section and add the following flags to `Other Swift Flags` section: `-Xfrontend -enable-experimental-concurrency`.

##### 4. Import experimental concurrency module

Add `import _Concurrency` to the header of the file that you are going to use for experimenting the feature. Note the underscore (\_) in the beginning of the name of the module which indicates that it's yet under development.

Done! You can now use `async`/`await` in your code. Xcode won't highlight them yet but it will nicely compile and won't block your way.

### How to use async/await

By the time this feature is released, it's quite likely that Apple will have converted some (hopefully many) asynchronous methods to `async`, but right now, we need to build our own `async` method. To do that, we will use a helper that comes with the `_Concurrency` module, that is, `withUnsafeContinuation`. We'll come to that in a second.

#### The "sad" code (what we already do)

Assuming what you see below is the code you want to rewrite using `async`/`await` because you are unhappy with it (because of the problems we mentioned above):

```
class SendSelectedPhotoWithEmailViewController: UIViewController {
    func startFlow() {
        // Get access to user's photo library, when the access is granted, display photo picker.
        // When the user picks a photo, first upload it to the server, then send the uploaded file URL
        // via email. Finally, show the user the result of the process.
        PhotosAPI.grantPhotosAccess { didGrantAccess in
            if didGrantAccess {
                PhotosAPI.selectPhoto { selectedPhoto in
                    NetworkAPI.upload(image: selectedPhoto) { uploadedFileUrl in
                        NetworkAPI.sendUrlWithEmail(urlString: uploadedFileUrl) { [weak self] didSend in
                            self?.displayResultAlert(didSendEmail: didSend)
                        }
                    }
                }
            }
        }
    }
}
```

We will not convert every method above since the logic is almost the same. We will just convert one for demonstration and refactor the other lines as if the corresponding methods were also converted.

Let's pick `NetworkAPI.upload(image:completion:)` method. If we were mocking the API and faking the network delay using `sleep`, the method would be something like this:

```
static func upload(image: UIImage, completion: @escaping (String) -> Void) {
    DispatchQueue.main.async {
        sleep(1) // Fake wait
        completion("fake URL")
    }
}
```

- Problem 1: Our method deals with asynchronous code so it cannot return anything. That's why it takes a closure where it passes the result.
- Problem 2: When we use the trailing closure syntax in the place we call this method and embed such methods within each other, things get out of control.

#### The "happy" code (`async`/`await`)

Here, let's create a method step by step, keeping the problem duo above in mind, and hopefully fix them.

We start with the method signature. If we want to get rid of the closure, we need our method to return the value that we pass into the closure. Something like:

```
static func upload(image: UIImage) -> String
```

Now, when we create the method body, the compiler will say "`Missing return in a function expected to return 'String'`".

This is fine. That's what we're aiming to fix with `async`/`await`. But how?

#### withUnsafeContinuation

We can think of `withUnsafeContinuation` as a wrapper that you use around your asynchronous code which blocks the thread until you explicitly resume. If you write tests for your asynchronous code, you should be familiar with this pattern from using `XCTestExpectation` and its `fulfill` method.

```
static func upload(image: UIImage) -> String {
    withUnsafeContinuation { continuation in // Compiler error 1: "'async' in a function that does not support concurrency"
        DispatchQueue.main.async {
            sleep(1) // Fake wait
            completion("fake URL") // Compiler error 2: "Cannot find 'completion' in scope"
        }
    }
}
```

So the compiler is not yet content. The second error is something we are familar with, but the first one is new: "`'async' in a function that does not support concurrency`".

My developer instincts tell me to mark this function somehow to tell the compiler that it supports concurrency. Maybe make it more explicit that my function is... `async`.

So changing from method signature from

```
static func upload(image: UIImage) -> String
```

to

```
static func upload(image: UIImage) async -> String
```

actually works! Why did I put `async` there and not somewhere else in the signature? Well, maybe I tried different places until it doesn't produce an error, who knows!

For the second error, "`Cannot find 'completion' in scope`", I'm just going to go ahead and remove that line. Sometimes you just need to ignore your problems until they're gone, right?

We have a new compiler error, "`Call is 'async' but is not marked with 'await'`" but it's so easy to fix that even Xcode itself offers a solution. We just mark our call with `await`.

Here's how our method looks now:

```
static func upload(image: UIImage) async -> String {
    await withUnsafeContinuation { continuation in
        DispatchQueue.main.async {
            sleep(1) // Fake wait
        }
    }
}
```

We just wrote a couple of lines and barely did what we wanted to, but our [code compiles](https://twitter.com/kaydacode/status/1345116211462418432/photo/1)... Some things are missing, right? Like a puzzle. Let's go one by one:

1. Our method signature returns a `String`, but the method body doesn't return anything yet it still compiles.

The answer is Swift 5.1, or more specifically, "Implicit returns from single-expression functions". So our method actually does return something, and that is the result of the `withUnsafeContinuation` method. But maybe it's a good idea to make `return` explicit to avoid confusion.

2. In the original method, we would return a string using the completion block, here we just erased it and didn't put anything else instead.

Fair point. But we can't just return something in the body of `DispatchQueue.main.async` ("`Unexpected non-void return value in void function`"), so there must be a different way of doing that. Maybe calling a method?

3. `withUnsafeContinuation` passes a value of type `UnsafeContinuation` into its closure but we never use it. What is it for?

There's only one way to figure it out: To play with it. When we try to use it, we see that it has only one method available, which is `continuation.resume(returning:)`. Could it be the method we were looking for in the previous problem?

Now we can finalize our method using `continuation.resume(returning:)`. The complete version looks like this:

```
static func upload(image: UIImage) async -> String {
    return await withUnsafeContinuation { continuation in
        DispatchQueue.main.async {
            sleep(1) // Fake wait
            continuation.resume(returning: "fake URL")
        }
    }
}
```

Time to get a pulse-check. In the "sad" code above, we mentioned two problems so let's refresh our memories and note down our progress:

- Problem 1: Our method deals with asynchronous code so it cannot return anything. That's why it takes a closure where it passes the result.

This is now fixed as the method returns a `String` in the signature and the method body is updated accordingly.

- Problem 2: When we use the trailing closure syntax in the place we call this method and embed such methods within each other, things get out of control.

This is not yet fixed because we didn't refactor the part we call this method, but it doesn't take a closure anymore so we made good progress there.

It's time to use our new method, so let's scrap everything in `startFlow()` and just call this new method:

```
func startFlow() {
    let url = NetworkAPI.upload(image: UIImage(named: "an-image")!) // Compiler error: "'async' in a function that does not support concurrency"
}
```

Well, we are familiar with the compiler error but something different happens when we use Xcode suggestion to correct this: The method gets marked with `@asyncHandler`. Okay, our method is literally an async handler so the signature feels right, let's leave it as it is. And the new error "`Call is 'async' but is not marked with 'await'`" is also easy to fix, we just mark our call with `await` and we have the following:

```
@asyncHandler func startFlow() {
    let url = await NetworkAPI.upload(image: UIImage(named: "an-image")!)
}
```

Everything seems to work. So, if we were to use the same syntax for our other methods, the final state of `startFlow()` would be something like this:

```
@asyncHandler func startFlow() {
    guard await PhotosAPI.grantPhotosAccess() else { return }
    let selectedPhoto = await PhotosAPI.selectPhoto()
    let uploadedFileUrl = await NetworkAPI.upload(image: selectedPhoto)
    let didSendUrlWithEmail = await NetworkAPI.sendUrlWithEmail(urlString: uploadedFileUrl)
    self.displayResultAlert(didSendEmail: didSendUrlWithEmail)
}
```

What do you think? Much cleaner, right? There's one last thing we didn't talk about, that is `@asyncHandler`.

#### @asyncHandler

We can think of `@asyncHandler` like a stamp you use in the method signature to tell the compiler that your method does not return anything but just handles other `async` functions. When you mark your method with `@asyncHandler`, you can use it in other methods without having to mark them with `@asyncHandler` or `async`. Consider `@asyncHandler` as the last wrapper in your `async` code because eventually, you need to call these methods from a method that you can't touch the signature, for instance, from `UIViewController` overrides.

## Final words

As a final recap, let's see the code before `async`/`await`:

![Swift Pyramid of Doom](/assets/img/posts/2021/swift-async-await/swift-pyramid-of-doom-1024x347.png)Here's the code with `async`/`await`:

![Swift Async Await](/assets/img/posts/2021/swift-async-await/swift-async-await-1024x202.png)As you can see, the new code is almost no different than a synchronous code block but the code is more readable and more manageable compared to the one with closures, thanks to `async`/`await`. I hope this post helped you get familiar with how `async`/`await` works and you are as excited about the future of concurrency in Swift as I am. Feel free to leave a comment on this post or [send me a tweet on Twitter](https://twitter.com/kublaios).

Oh, and for the full code, check out [my gist on Github](https://gist.github.com/kublaios/0ec48aeb0ff353a8ad59747f52f5ea75).

## References

- Special thanks to [Vincent Pradeilles](https://www.youtube.com/channel/UCjkoQk5fOk6lH-shlm53vlw) on YouTube who helped me get familiar with this concept in [his video](https://www.youtube.com/watch?v=C0tx4Cionus).
- [github.com/apple/swift-evolution - SE-0296 Async/Await](https://github.com/apple/swift-evolution/blob/main/proposals/0296-async-await.md)
- [forums.swift.org - Accepted with Modification SE-0296](https://forums.swift.org/t/accepted-with-modification-se-0296-async-await/43318)
- [forums.swift.org - Concurrency Asynchronous Functions](https://forums.swift.org/t/concurrency-asynchronous-functions/41619/3)
- [forums.swift.org - Pitch 2 Async/Await](https://forums.swift.org/t/pitch-2-async-await/42420)
- [https://en.wikipedia.org/wiki/Swift\_(programming\_language)](https://en.wikipedia.org/wiki/Swift_(programming_language))
