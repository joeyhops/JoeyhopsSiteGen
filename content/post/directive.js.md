+++
categories = ["Directive.js", "Programming"]
date = 2020-10-27T19:42:00Z
description = "Directive.js Introduction"
tags = ["javascript", "side-project", "preprocessor", "typescript", "js", "directive"]
title = "Introducing Directive.js"

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

Combined with the above directive (assuming the scope is correct, more on that later), the preprocessor would check to see if the identifier "EXAMPLE" has a defined value. If it does, the preprocessor will transform the source code to ensure the code BELOW the conditional directive (ifdef in this example) UP TO the end directive (endif in this example) is INCLUDED in the transformed file. Otherwise, if the condition is FALSE (EXAMPLE is undefined) the code in-between the directive and the end-directive is excluded from the transformed file.

From this starting point, I began tossing the idea of a TS/JS Preprocessor around in my mind, thinking about the various uses of such a tool. Eventually I expanded the idea of a preprocessor past the basics of the C-Preprocessor.

## The Concept Of Scope

![](https://cdn-media-1.freecodecamp.org/images/1*lskdwh7Th3ug538lVYUscQ.png)

The most interesting feature, I believe, that Directive.js has over the C-Preprocessor, is the concept of "Directive Scope". "Directive Scope" Refers the environment and state of the running preprocessor as it modifies the files. One of the primary uses of the C-Preprocessor is to define constants, values with a static unchanging value accessible as a Macro to C programs that include the file the defined the constant.

Example:

     #define EXAMPLE 10

Assuming the above example is located in a file named 'constants.h', any C file that includes the 'constants.h' file has access to the EXAMPLE constant and value.

Directive.js has a similar system, having two Scope objects that hold the data and variables defined using directives. Where Directive and C differ, however, is in Directives use of Scope levels. Directive has two levels of Scope at any given point during the processing stage. These two levels are:

* Local Scope
* Global Scope

With each level having different rules regarding access.

### Local Scope - Per File Definitions

![](https://images.code.org/76e9fc4059384cca87c45b7e3647ae59-image-1446407129001.png)

A Scope object, at it's heart, is a simple JS object that holds Key-Value pairs for variables defined by directives. These variables can be used by other Directives to control the transformation and output of preprocessed source files. Local Scope holds variables temporarily when parsing/transforming a file, clearing it when a new file is staged for processing. Variables defined in Local Scope are seen only by the directives in the current file, and functionally no longer exist once that file has been processed.

Most Directive variables will work using Local scope, however if a more persistent solution is required;

### Global Scope - Global Variables & Values

![](https://images.code.org/ac720d8ae4d6380fc72c8d6659910bcf-image-1446407286955.png)

The Global Scope is where things get particularly interesting. Not only are Directive variables defined globally accessible to ALL files being processed by Directive, but Global variables can also be accessed directly in-source without using a directive. By including the GlobalScope library in their project, a user can call out to the preprocessor at processing time, accessing variables defined globally in the directives, the values of which are replaced during the transformation stage and outputted as standard JS values for compilation.

Scope values are limited to the following types (for both Local and Global Scope values):

* Strings
* Numbers
* Booleans
* JSON Objects

The final being a rather peculiar feature of Directive.js.

## Object In Scope - JSON Headers

![](https://d2tlksottdg9m1.cloudfront.net/uploads/2019/02/JSONSample.jpg)

While the concept of having a file-independent object of Keys/Values to use globally in your directives and source is pretty neat, having it limited to Strings, Numbers, and Bools would be kind of lame. That's when I had the idea of directive that would take and parse a JSON file, injecting the entire object into the Global Scope object along with all it's values and sub-objects. This allows users to easily include JSON files in their project for configuration, state management, etc. entirely globally without the compiler ever seeing the JSON file. The object is injected directly into the source on output if the entire object is required, otherwise, during the transform phase of the directive, the value of the global object being referred to is replaced with the value directly, allowing for conditional logic and value manipulation without ever including unused fields/values in the final build.

## Directive.js Is In Development

Honestly, I have no idea if this is actually useful. The original idea stemmed from my own personal desire to be able to conditionally include/exclude code snippets using a simple syntax similar to the C-Preprocessor. Expanding on this idea, I took the concept of DocComments (Comments that use specific notation to generate project documentation based on code comments) and with a little bit of personal flair, came up with the idea for Directive.js. The concept for Scope grew naturally out of a need to manage the data being defined, accessed, and manipulated by directives, with the idea for a GlobalScope of variables that exists outside of the raw source code following directly from that base idea. Either way, it's an interesting idea and something I would most certainly (at least try to) use personally, and it's for that reason that I'm currently in the process of writing V0.0.1 of Directive.js.

Currently, I have a few things on my TODO list that I consider "MVP" for Directive:

* Working Scope
  * Local Scope \~ In Progress
  * Global Scope \~ In Progress
    * Serializable to file
* Working Directives
  * Base parsing/matching \~ Complete
  * Accessing & Modifying Scope
    * Global
    * Local
  * Conditionals
* Standard (Built-in directives):
  1. define -> Define Locally-Scoped variable \~ In-Progress
  2. define-global -> define Globally-Scope variables
  3. global -> Include JSON Object in global scope
  4. if/ifdef/ifndef -> basic conditionals
* User-Defined Directives

Which I'll need to complete before I even consider publishing this package to NPM. For now the repo is private on my GitHub, though I'll be making it public very shortly. You can find the repo here:

[Directive.js](https://github.com/joeyhops/directive.js "Directive.js")

Check back soon for more updates - I'd like to start a running DevLog for this project, though my follow through always leaves something to be desired. I'll make sure to post at least a short blog post when the repo goes public though, if not something more. I've got a lot of ideas for this project, I think it could definitely be something at least slightly useful, so if you want to follow the development of Directive.js, offer feedback, tips, support, complaints, or anything really please don't hesitate to email me at me(at)joeyhops.com

You can also [follow me on GitHub](https://github.com/joeyhops "Follow me on github") if you want to keep an eye on my open source projects.