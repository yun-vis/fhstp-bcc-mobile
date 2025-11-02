---
# permalink: /404.html
layout: single
classes: wide
title: "UI"
header:
  image: /assets/images/teaser/teaser.png
  caption: "Image credit: [**Yun**](http://yun-vis.net)"
last_modified_at: 2025-10-31
---

# UI

- [Compose]()
  
- [State](mutable state)
  
- [Scaffold](https://developer.android.com/develop/ui/compose/quick-guides/content/create-scaffold): In Material Design, a scaffold is a fundamental structure that provides a standardized platform for complex user interfaces. It holds together different parts of the UI, such as app bars and floating action buttons, giving apps a coherent look and feel.

## UI 

- Compose, close to react
- Compose phases: Data -> Composition -> Layout -> Drawing -> UI
- Column, Row, Box (on top of each other)
- dimen is a resource type [Link](https://developer.android.com/guide/topics/resources/more-resources)
- Add new vector asset 
- Alt+Enter -> Show context actions
- Declarative Paradigm

# Rules of Composables [Link](https://mrmans0n.github.io/compose-rules/rules/)

# Compose 

- [Thinking in Compose](https://developer.android.com/develop/ui/compose/mental-model)
  - (1) Describe what, not how. (2) UI elements are functions. (3) State controls UI. (4) Event contols State
  - Declarative programming paradigm: Imperative programming involves writing step-by-step instructions for a computer to execute, focusing on the "how". Declarative programming focuses on "what" the desired result is, without explicitly detailing the process, leaving the "how" to the system. 
  - In compose, UI elements are functions, and not objects. UI elements are controlled by the states or arguments that you pass. How to update state to update the UI? through events. The event handler should decideif the UI state should be changed. If the UI state changes, the functions or UI elements that depend on that state will be re-executed. The process of regenerating the UI when the state changes is called recomposition.
  - [UI widgets](https://developer.android.com/design/ui/widget): The building blocks of a user interface (UI) that are used to create applications. Buttons, text fields, checkboxes, sliders, and cards are all examples of UI widgets.
  - [mutate view](): Mutating methods are ones that change the object after the method has been used. Non-mutating methods do not change the object after the method has been used.

## Compose State
- Stateless/Statefull components

## Adding Resources

## Image resources

# UI II

## Navigation and Layout 
- [Scaffold](https://developer.android.com/develop/ui/compose/components/scaffold)
  The scaffold composable provides a straightforward API that once can quickly assemble an app structure according to the Material Design Guidelines.

## Intent
- Component to start activities

## ViewModel
- Flows
- state flow

## Architecture Components
- [ViewModels and State in Compose](https://developer.android.com/courses/pathways/android-basics-compose-unit-4-pathway-1)
- [Navigation in Compose](https://developer.android.com/courses/pathways/android-basics-compose-unit-4-pathway-2)
- [Architecture](https://developer.android.com/develop/ui/compose/architecture)
- [State hositing](https://developer.android.com/develop/ui/compose/state-hoisting)

# External Resources

- [Compose Mental Model](https://developer.android.com/develop/ui/compose/mental-model)
- [Android Basics with Compose](https://developer.android.com/courses/android-basics-compose/course)
- [Jetpack Compose for Android Developers](https://developer.android.com/courses/jetpack-compose/course)
- [Material Design 3](https://m3.material.io/) 
  Material Design 3 is Google’s open-source design system for building beautiful, usable products.

# Terminology

- Unidirectional Data Flow: Unidirectional data flow is a design pattern where data flows in a single direction, typically from parent to child components. This pattern works with state flowing down from a source of truth to the UI, and events flowing up from the UI to the source of truth to request a change. 

- Modifier: Compose uses a Modifier object, which is a collection of elements that decorate or modify the behavior of Compose UI elements. 

- Annotation: Annotations are a means of attaching metadata to code. They don't change how your program runs, but they give extra information to the compiler or tools. The function is annotated with the @Composable annotation. All composable functions must have this annotation. This annotation informs the Compose compiler that this function is intended to convert data into UI.

- Idempotent: A function is idempotent if applying it multiple times to the same input produces the same result as applying it just once. The function behaves the same way when called multiple times with the same argument, and it does not use other values such as global variables or calls to random().

- Side-effect: A side-effect is any change that is visible to the rest of your app. For example, these actions are all dangerous side-effects: (1) Writing to a property of a shared object, (2) Updating an observable in ViewModel, (3) Updating shared preferences.

- [Recomposition](https://developer.android.com/develop/ui/compose/mental-model#recomposition): In an imperative UI model, to change a widget, you call a setter on the widget to change its internal state. In Compose, you call the composable function again with new data. Doing so causes the function to be recomposed--the widgets emitted by the function are redrawn, if necessary, with new data. The Compose framework can intelligently recompose only the components that changed.

- Dynamic content(): Because composable functions are written in Kotlin instead of XML, they can be as dynamic as any other Kotlin code. 