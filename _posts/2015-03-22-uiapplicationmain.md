---
layout: post
title:  "@UIApplicationMain"
date:   2015-03-22 16:00:00
permalink: /4
---
Today I learned that [Xcode 6.2](https://developer.apple.com/library/ios/releasenotes/DeveloperTools/RN-Xcode/Chapters/xc6_release_notes.html) no longer creates `main.swift` for new projects. Traditionally the main file has only contained a few lines of code that called `UIApplicationMain` to bootstrap the application. That's all. It's 100% pure boilerplate which Apple has now removed. Tagging app delegate using the freshly introduced `@UIApplicationMain` takes care of all the boilerplate needed for bootstrapping the application.

In those rare cases when you do want to execute some code in the main, it's still possible to add  `main.swift` to the project manually and bootstrap the application the old fashioned way. Just remember to delete the attribute from the application delegate while doing that.

In Xcode 6, Apple dropped generating `.pcx` header by default. I think that was a change for better. The header usually gathered bloat such as logging macros, imports for all *important* libraries, favorite multipurpose macros of every developer in the project. It's convenient but it pollutes the namespace (especially without prefixes) and slows down the compilation. Also I feel it's a bad practice in general because it doesn't help to keep the codebase clean.

I hope Apple continues the trend of drop boilerplate and simplify the development while doing so. Kudos to Apple.

PS. For OS X the equivalent tag is `@NSApplicationMain`
