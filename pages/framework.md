---
# permalink: /404.html
layout: single
classes: wide
title: "Framework"
header:
  image: /assets/images/teaser/teaser.png
  caption: "Image credit: [**Yun**](http://yun-vis.net)"
last_modified_at: 2025-11-01
---

# Andriod Framework

- [Activities](https://developer.android.com/guide/components/activities/intro-activities): abstract class, component shows on the screen, has a lifecycle
- Services: run in the background, no UI component, starting->running->destroyed, foreground, background, bound
- Intents: Component to start others, implicit vs explicit
- Broadcast receivers: responsible to system events
- Content providers
- 


# Andriod Architecture

- [Guide to app architecture](https://developer.android.com/topic/architecture)
  
- [UI Layer](https://developer.android.com/topic/architecture/ui-layer)
  The role of the UI is to display the application data on the screen and also to serve as the primary point of user interaction. Whenever the data changes, either due to user interaction (like pressing a button) or external input (like a network response), the UI should update to reflect those changes. Effectively, the UI is a visual representation of the application state as retrieved from the data layer.

- [Domain Layer](https://developer.android.com/topic/architecture/domain-layer)
  The domain layer (optional) is responsible for encapsulating complex business logic, or simple business logic that is reused by multiple ViewModels. This layer is optional because not all apps will have these requirements. You should only use it when needed-for example, to handle complexity or favor reusability.

- [Data Layer](https://developer.android.com/topic/architecture/data-layer)
  While the UI layer contains UI-related state and UI logic, the data layer contains application data and business logic. The business logic is what gives value to your app—it's made of real-world business rules that determine how application data must be created, stored, and changed.

- In Android, "data" can refer to the actual information stored (like user preferences or cached files), while a "data repository" is an architectural pattern that manages and abstracts access to this data from various sources, such as databases and network APIs. 

- When you need to perform a side-effect, trigger it from a callback.


# Terminology

- [Activity](https://developer.android.com/guide/components/activities/intro-activities): An Activity is an application component that provides a screen with which users can interact in order to do something.

- [Fragment](https://developer.android.com/guide/fragments): A Fragment represents a reusable portion of your app's UI. A fragment defines and manages its own layout, has its own lifecycle, and can handle its own input events. Fragments can't live on their own. They must be hosted by an activity or another fragment. The fragment’s view hierarchy becomes part of, or attaches to, the host’s view hierarchy.

- [Package](https://developer.android.com/build/configure-app-module): A package is a namespace that organizes a set of related classes and interfaces.

- [Callback](https://en.wikipedia.org/wiki/Callback_(computer_programming)): In computer programming, a callback is programming pattern in which a function reference is passed from one context (consumer) to another (provider) such that the provider can call the function. The concept of callbacks is to inform a class synchronous / asynchronous if some work in another class is done. An event handler is a type of callback.

- [App manifest](https://developer.android.com/guide/topics/manifest/manifest-intro): An app manifest is a file that describes an application's metadata, configuration, and requirements to an operating system, platform, or service. 

- [Form factors](https://developer.android.com/guide/navigation/responsive/form-factors): In computing, form factor refers to the size, configuration, shape, weight or physical arrangement of a computing device. For example, not just phones, but also tablets, foldables, ChromeOS devices.

- Business logic: Business logic is the set of rules, calculations, and algorithms that govern how a software application handles and processes data according to a business's real-world rules.

- Application Sandbox: The Android platform takes advantage of the Linux user-based protection to identify and isolate app resources. This isolates apps from each other.