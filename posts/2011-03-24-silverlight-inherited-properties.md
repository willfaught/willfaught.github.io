---
date: "2011-03-24T18:34:00+00:00"
draft: false
tags: ["silverlight", "fyi"]
title: "Silverlight inherited properties"
---
I was working on a Silverlight control template last night and wondered which dependency properties are inherited and which aren't. It's a good idea to bind those that aren't in the template so the dependency properties for UIElement, FrameworkElement, and Control work as expected for your control. However, I couldn't find a list of inherited dependency properties in the documentation or elsewhere online. I discovered a somewhat reliable way to determine such a list: Look at the documentation for counterparts in WPF. The WPF documentation indicates whether a dependency property is inherited in the *Dependency Property Information* section. If it's inherited in WPF, then it's likely that it's inherited in Silverlight too, since they try to keep things as compatible as possible.

