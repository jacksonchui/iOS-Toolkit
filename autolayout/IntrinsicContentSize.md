# Intrinsic Content Size

**`var intrinsicContentSize: CGSize`** is the default recommended size that iOS components are. It allows views to lay themselves out without requiring full constraints. 

*It is a recommendation - not a requirement*.

## Content Hugging and Compression Resistance

**Content Hugging** is a constraint on how much a view should grow/expand or "hug" its content.

**Compression Resistance** is a constraint on how much a view can be compressed or shrunk. It is necessary so content is not hidden.

To resolve constraint conflicts and ambiguity, you can set layout priorities. By setting a priority *lower*, the view says, "I am okay hugging less than other views on the screen, don't mind me."

Set priorities either vertically or horizontally, depending on what axis there are multiple elements on.

```swift
view.setContentHuggingPriority(UILayoutPriority(rawValue: 249, for: .horizontal))
```

### iOS Defaults

Anchors have a `struct UILayoutPriority` of 1000: they are required.

The priority for view CHCR is CH: `.defaultLow=250`, CR: `.defaultHigh=750`.

## Overriding `intrinsicContentSize`

```swift
class CustomView: UIView {
    override var intrinsicContentSize: CGSize {
        return CGSize(width: 100, height: 100)
    }
}
```

## Standard iOS Components and their Intrinsic Content Sizes

* `UIActivityIndicatorView` is either `UIActivityIndicatorView.Style.medium` (20,20) or `.large` (37,37).
* `UISwitch` is (49,31).
* `UIButton` is the size of the label hugged plus [`contentEdgeInsets: UIEdgeInset`](https://developer.apple.com/documentation/uikit/uibutton/1624036-contentedgeinsets).
* `UIImageView` is the size of the image or none if no image is set. It is best to add width and height anchors to achieve scaling.
* `UILabel` is the size that fits the text, with no constraint on the width or `UIView.noIntrinsicMetric`.
* `UIView` has no intrinsic content size.