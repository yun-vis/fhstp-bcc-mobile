---
# permalink: /404.html
layout: single
classes: wide
title: "Environment Setup"
header:
  image: /assets/images/teaser/teaser.png
  caption: "Image credit: [**Yun**](http://yun-vis.net)"
last_modified_at: 2025-08-22
---

# Installation

## Andriod Studio

- Version: Android Studio Narwhal [2025.1.2](https://developer.android.com/studio/releases)
  - AGP Version 4.0 or higher 

- Setup:
  - Step1: Open Andriod Studio (Download Android Studio Narwhal 3 Feature Drop)
  - Step2: Create a project by selecting New -> New Peoject -> Empty Activity
  - Step3: Package Name -> at.ac.fhstp.myfirstapp, it is usually done using the inverse of the company domain
  - Step4: Minimum SDK -> API 26 ("Oreo", Andriod 8.0)
    - API: Application Programming Interface (API) 
    - SDK: Software Development Kit (SDK) 
    - Gradle: Gradle is a build automation tool for multi-language software development. It manages tasks like compilation, packaging, testing, deployment, and publishing.
  - Step5: Press finish
  - Step6a: Device Manager -> add a new device (i.e, pixel 6a) -> API 35
  - Step6b: For physical device, enable the USB debugger mode (recommended, but need a decent USB cable)
  
## Usage

- double shift -> enable search -> search editor and preview -> enable 
- "@Composable" annotation draw UI
- double shift -> enable search -> search Presentation Assistant -> enable 
- double shift -> enable search -> search Font size -> enable change font size with Ctrl + mouse wheel in 
- andriod view -> res (resources)

## Shortcut
- Ctrl+Alt+L: auto formating
- Ctrl+Alt+B: Go to Implementation
- Ctrl+B: Go to Declaration
- Alt+Enter: Show quick fix
- Ctrl+Shift+O: Sync with Gradle or set auto syn

### Notes
- When it comes to navigation bar, one needs to run the simulator in stead of preview
- Copy paste codes does not alway produce valid codes, need to double check when typing


# Editor
- Word Wrap: Main Menu -> View -> Active Editor -> Soft-Wrap
- 

# Terminology

- Annotations: Annotations are a means of attaching metadata to code. [Link](https://kotlinlang.org/docs/annotations.html#)

- dp: [Density-independent pixels](https://developer.android.com/guide/topics/resources/more-resources#Dimension)

- Idempotent: In Android Jetpack Compose, an idempotent composable function is one that produces the same output for the same input, even if it is called multiple times during recomposition. 

- operation no side effect -> it does not alter any external state

- [Android Jetpack](https://developer.android.com/jetpack): Jetpack is a suite of libraries to help you follow best practices, reduce boilerplate code, and write code that works consistently across Android versions, devices, and form factors so you can focus on the code you care about.
  
- [Modern Android Development (MAD) Skills]: Learn about best practices and recommended Architecture for building robust, production-quality apps.

# Mobile Desktop Sharing

- [scrcpy](https://scrcpy.org/)
- [scrcpy github](https://github.com/Genymobile/scrcpy/)
