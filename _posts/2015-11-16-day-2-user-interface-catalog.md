---
title: "Day 2: Learn standard user interface from UI catalog"
---

###### Environment

- OS X 10.11.1
- Xcode 7.1.1

There are many custom ui library in the world, or you create any ineterface.
But I recommend for your first app, Use starndaru UI created by Apple.

The UI framework for iOS named UIKit, We can learn from the flollowing links:

#### Online Document
<https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/index.html#//apple_ref/doc/uid/TP40012857>

#### Example
<https://developer.apple.com/library/ios/samplecode/UICatalog/Introduction/Intro.html#//apple_ref/doc/uid/DTS40007710>

##### How to Run

1. `Download Sample Code`
1. Extract `UIKitCatalogiOSCreatingandCustomizingUIKitControls.zip`.
1. Open `UIKitCatalogiOSCreatingandCustomizingUIKitControls/Swift/UIKitCatalog.xcodeproj`.
1. Press `Command + R`.
  ![](/images/ui_catalog/1-launch_ui_catalog.png)

As seen from the above, apple provides many internface parts in default. Maybe, it's enaough for your first app.

# Example: Create a simple notes app

You can create a simple notes with standard parts.

Feature | UI parts
--- | ---
Manage views | UINavigationController
Notes list | UITableViewController, UITableView, UITableViewCell, UILabel
Show or edit note | UIViewController, UITextView

If you understand UIKit, you can create many app without unnecessary customizations.
I recommend see all UI in calalog at least once.

