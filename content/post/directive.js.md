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

## ![](https://media.geeksforgeeks.org/wp-content/cdn-uploads/Preprocessor-In-C.png "courtesy: geeksforgeeks.org")

## What is Directive.js?

I work, full-time, on a piece of software primarily written in TypeScript/Javascript (we also use PowerShell but that's neither here nor there). The details aren't important, but suffice it to say that it's a React-based project with a relatively intense build process managed through Gulp. This is due to the framework we're required to work within, and has provided several pieces of inspiration for developer-oriented utilities that I, personally, would find exceedingly useful, specifically for this aforementioned project.

Directive.js is one such utility I've been tossing around/searching for/thinking about for a long time now. The idea is a simple one; A preprocessor with directives similar to the ones available to C developers for JS/TS developers. Naturally the preprocessor cannot be a simple 1:1 translation of what's available to C, as the C-Preprocessor is a very specific tool for the C-language family, being born both of necessity and utility. For this reason, I've come up with (what I consider) a simple to use Library, Preprocessor, and Syntax for inclusion in your projects.

The idea is simple, before any other compilation/transformation steps are run on your source (be it Babel, Gulp transforms, TSC, etc.) iterate through all files (as per a directive config file with inclusion options), extract all "Directive Comments" where a "Directive Comment" is a comment that begins immediately with a '#' symbol followed by the name of a directive, as well as any other parameters required by said directive.

Example:

    //# define EXAMPLE"My Example Var"

The above would "DEFINE" the identifier "EXAMPLE" with the value "My Example Var" which can then be used by other directives for the use of source management, environment management, conditional compilation, and more. An example of this would be:

    //# ifdef EXAMPLE
    *SOURCE CODE HERE*
    //# endif

Combined with the above directive (assuming the scope is correct, more on that later), the preprocessor would check to see if the identifier "EXAMPLE" has a defined value. If it does, the preprocessor will transform the source code to ensure the code BELOW the conditional directive (ifdef in this example) UP TO the end directive (endif in this example) is INCLUDED in the transformed file. Otherwise, if the condition is FALSE (EXAMPLE is undefined)