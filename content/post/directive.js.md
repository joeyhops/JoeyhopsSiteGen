+++
categories = ["Programming", "DevLogs"]
date = 2020-10-27T19:42:00Z
description = "DevLog For Directive.js - Day 1"
draft = true
tags = ["javascript", "side-project", "preprocessor", "typescript", "js", "directive"]
title = "Directive.js"

+++
# Introducing Directive.JS

### A comment based preprocessor/macro/transformation manager for your JS/TS Projects

![](/directive.png)

If you've ever worked with C or C++, then you've definitely experienced the power of the C-Preprocessor firsthand. Perhaps you haven't been one to leverage the power afforded to you by the C-Preprocessor, or maybe you're a macro-god with header files that would make us mere mortals weep. Either way, no matter how you use it, the C-Preprocessor is one of it's most valuable tools. Allowing you to define constants, macros, interact with functions and values in your source, include files/libraries, etc. All without requiring any extra compilation steps, rather all this logic is encapsulated in what could be considered a tiny DSL that is parsed and expanded before compilation takes place. The C-Preprocessor modifies the source file directly, transforming the code for the compiler before the compilation step takes place. This allows developers to do things like conditionally compile code depending on the platform/features available. It also allows developers to define strong constant values that exist globally in the project - assuming the file is included, that is - as well as expand macros that can be combined with source functions for powerful utilities. C-Preprocessor statements are known as "Directives" and can only exist on a single line in a C source file (semantically, they can be split over multiple lines using '\\' line breaks, however this is purely for the developer, the preprocessor only understands single line directives).

![](https://media.geeksforgeeks.org/wp-content/cdn-uploads/Preprocessor-In-C.png "courtesy: geeksforgeeks.org")