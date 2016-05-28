---
layout: post
title:  "Swift flips long-standing Objective-C Cocoa error conventions"
categories: blog
---

[Tony Parker][tony] provided this nugget of gold on the [swift-corelibs-dev][mailing-list] mailing list with regards to translating Objective-C APIs and behaviors to Swift.

> Unfortunately the `throws` syntax in Swift often causes a mixup between two different things, because it flipped the terminology from what all of our documentation and header comments use.

> 1) Cocoa uses exceptions (`@throw` in ObjC) to indicate programmer errors and they are generally not intended to be recoverable.  Example: passing nil where not expected, passing an invalid argument, failing to meet a precondition of an API.

> 2) Cocoa uses `NSError **` to indicate runtime errors that are recoverable or at least presentable to user. Example: out of disk space, name of file already exists.

> The ‘throws’ syntax in Swift is actually for case #2, not #1. In Swift, #1 is `fatalError` or preconditionFailure. #2 is `throw Error`.

There are [reasons][reasons] why exceptions were not used in Cocoa to handle runtime errors, also `NSError` [predates][predates] native exception handling in Objective C.

Swift `throws` is safer and more conventional. It's safer given that you have to deal with the error,  you can't accidentally ignore it. It's more conventional given that's how other languages communicate potentially recoverable runtime errors. The change in convention may irritate some of us who are  Apple developers, but for someone joining the fray from another language or platform it feels more like home.

[tony]: https://github.com/parkera
[mailing-list]: https://lists.swift.org/pipermail/swift-corelibs-dev/Week-of-Mon-20160509/000631.html
[reasons]: http://stackoverflow.com/questions/4648952/objective-c-exceptions
[predates]: https://www.bignerdranch.com/blog/error-handling-in-swift-2/
