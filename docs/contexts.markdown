---
layout: default
title: Contexts
permalink: contexts
---

Contexts are a tool which describe what should be assumed. It allows us describe and define our models without adding too many details which makes our explanations noisy and harder to grok.

Instead of contexts always being implicit, Elder allows developers to model them explicitly so they can adapt to different sets of assumptions, handle specificity with inheritance, decide how specific matching conditions decide which contexts should be used, etc.

Some constucts are implicitly a new context. A commonly used context is a Type. Using it's context, a Type can customize it's assumptions like order of evaluation, default interpretation, default values, and much more. This is how Types can customize what their contents semantically mean while keeping a relatively minimal syntax.

TODO:
* Global, universal context
* All the various types of implicit contexts
  * Context based on file types
* How to know, and define, what values a a context uses
  * eg name-value
  * eg partially-apply a function
* 
