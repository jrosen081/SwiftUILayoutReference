# Layout in SwiftUI
**By Jack Rosen**

In 2019, Apple released a radically different framework for making apps: SwiftUI. It is a reactive framework, meaning that any changes in your state are automatically re-rendered in the view. It is also a declarative framework, meaning that you say how the view should look, and SwiftUI does the hard work in making the views look as expected on the screen. Since this is such a different change from the previous app-making experience using UIKit, it can be confusing to figure out how SwiftUI makes decisions on how to display the view. That is the purpose of this document: to explain the layout system.

## Prerequisites

To best understand this article, you should be familiar with [Swift](https://www.geeksforgeeks.org/introduction-to-swift-programming/) and [UIKit](https://developer.apple.com/documentation/uikit). I will show code snippets using SwiftUI, so familiarity with Swift is important. I will reference certain different types of UIKit views to show similarities between UIKit and SwiftUI. Also, a basic understanding of how SwiftUI views are set up would be helpful to understanding this article. I would recommend reading the first few sections of this [website](https://www.answertopia.com/swiftui/creating-custom-views-with-swiftui/) to understand this topic.

## View Types

In SwiftUI, views are composed of other views. What this allows you to do is define views using other views. For example, here is a simple view that shows the text "Hello World".

```swift
struct HelloWorldView: View {
    var body: some View {
        Text("Hello World")
    }
}
```

It is important to know how the size and layout of this view are determined. It is a 3 step process:
1. A parent view (in this case `HelloWorldView`) proposes a view size to the child (in this case `Text`)
2. The child lays itself out using that size as a guide
3. The parent respects the child's size, and lays it out in its current coordinates.

### Wait, Respecting the Child's Size?

You might be confused as to what this means, so let me explain with the `HelloWorldView`. The `HelloWorldView` gives the `Text` the proposed size of the whole screen. The `Text` decides it needs a much smaller region, and then lets the `HelloWorldView` know how much space it needs. The `HelloWorldView` respects that decision, meaning that it gives the `Text` all the space it needs, and then places the `Text` in the center of the `HelloWorldView`.

### The 3 Types of Views

While this may seem very simple, there is a hidden complexity in the second step. Not all views respond to that step in the same way. There are 3 types of views:
1. Greedy views
2. Intrinsic views
3. Good Citizen views

Each specific base SwiftUI view is one of these types of views. The views you use will change the layout of the screen. I will go through each one of these in depth, since depending on what you use, you will have very different experiences.

### Greedy Views

Greedy views are, well, greedy! They take up all of the space given to them, no matter what. Some examples of these are `Color` and `Spacer`. As an example, if I want to make a view fully green I would do the following.

<img width="700" alt="image" src="https://user-images.githubusercontent.com/20172570/136574200-11d8d0fd-08cb-4d44-bb95-ba11fe806f6d.png">

The parent view (the whole view in this scenario), proposed the whole size to the color view. The Color view, being a greedy view, sized itself to take up the whole space.

### Intrinsic Views

Intrinsic views are views that take up their intrinsic content size (similar to how UIKit has an `intrinsicContentSize`). This means that they ignore the proposed size and instead pick whatever size they need to be drawn. An example of this is `Toggle` (the SwiftUI equivalent of `UISwitch`).

<img width="696" alt="image" src="https://user-images.githubusercontent.com/20172570/136575592-3a52a976-fbea-49c3-b30e-88d5def7be8f.png">

In this example, you can see the black border around where the SwiftUI layout view is, but it is clear that the `Toggle` still escapes this border. it ignores the amount of space given (20 in width from the `frame` modifier) and takes up as much space as needed to make sure it has enough space to render itself.

### Good Citizen Views

The final type of view is a good citizen view. What that means is that these views will use as much space as needed to lay themselves out, but never be bigger than the amount of space given from its parent. One of the best examples of this is `Text`, which renders text on the screen.

<img width="720" alt="image" src="https://user-images.githubusercontent.com/20172570/136576281-20468bcc-d98c-44b9-8410-a0c86793ebfb.png">

There are 3 different texts used in this example. The first one is short, so it fits on a single line. The second is longer, so it needs more than one line. The final one is long, but only allowed to have a height of 20 and width of 100, so it is truncated. This shows that it adapts to its given size and will truncate if needed.

### VStack?

What you may be asking now is, "Wait, what was that VStack thing that you used in your last example?". I might have misled you in terms of there being 3 views, since there really is a 4th type of view, a container view. The three built-in container views are `VStack`, `HStack`, and `ZStack`. They all work in similar ways, on the different axes, so we will focus on `VStack`. Stacks lay out their children with a multi-step process:
1. Split up the proposed area into n sections (n being the amount of children)
2. Offer one section's proposed area to a child
3. Use the size returned by the child and remove that area from the available space
4. Repeat stepss 1-3 until there are no children left to layout.

It becomes interesting when different types of views are used and combined with a stack, since everything you learned so far will come together.

### Exercise

Here is an example of a complex layout. See if you can figure out why the views layout as they do!

<img width="724" alt="image" src="https://user-images.githubusercontent.com/20172570/136578820-54a2a34d-a276-406d-9863-8c8a2adf3edd.png">

## Conclusion

This was a basic introduction into the layout system for SwiftUI. I hope it explained about the different types of views and how they interact. If you would like some more resources, here are some helpful links that explain more: https://www.swiftbysundell.com/articles/swiftui-layout-system-guide-part-1/ and https://crystalminds.medium.com/introducing-the-swiftui-layout-system-part-i-the-basics-42083aaaa5c7. Both of these give a great introduction into the layout system, with the first focusing more on how stacks (such as VStack) affect the layout, while the second focuses on how modifiers to views affect the view's sizing.
