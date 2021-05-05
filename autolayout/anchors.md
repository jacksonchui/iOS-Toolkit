# Anchors: `NSLayoutAnchor`

Anchors are the main tool that you will use for autolayout. 

The main subclasses of anchors are:

* `NSLayoutDimension`: This subclass is used to create layout constraints that describe the width and height of a view.
* `NSLayoutXAxisAnchor`: This subclass is used to create horizontal layout constraints.
* `NSLayoutYAxisAnchor`: This subclass is used to create vertical layout constraints.

`NSLayoutAnchor` will do type checking at compile time and warn you of any potental problems. You should see a *purple warning sign*.Anchors of views have `priority = UILayoutPriority(rawValue: 1000)`. If you have any issues with ambiguities, it is likely due to this and/or CHCR.

Make sure to make `translatesautoresizingmaskintoconstraints = false` for each view so that your app knows to use your constraints, otherwise you might be wondering why nothing applies or there are a ton of errors.

## Baseline: first and last

Baseline anchors are align the bottom of text-based Anchors to each other. `.firstBaselineAnchor` or `.lastBaselineAnchor` are the two options to activate.

Use as an alternative for `top` and `bottom` constraints.

``` swift
func setupViews() {
    let smallLabel = makeLabel(withText: "small", size: 12)
    let largeLabel = makeLabel(withText: "large", size: 20)

    view.addSubview(smallLabel)
    smallLabel.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor, constant: 8).isActive = true
    smallLabel.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 8).isActive = true

    view.addSubview(largeLabel)
    largeLabel.firstBaselineAnchor.constraint(equalTo: smallLabel.firstBaselineAnchor).isActive = true
    largeLabel.leadingAnchor.constraint(equalTo: smallLabel.trailingAnchor, constant: 8).isActive = true
}

func makeLabel(withText text: String, size: CGFloat) -> UILabel {
    let label = UILabel()
    label.translatesAutoresizingMaskIntoConstraints = false // important
    label.font = UIFont.systemFont(ofSize: size)
    label.text = text
    label.backgroundColor = .yellow
    
    return label
}
```

![](image/baselineAnchorExample.png)

## Pinning

NOTE: Be careful with pinning and make sure the constraints that you set don't conflict.

Pinning allows you to dynamically set the height and width of objects. Defining a top and bottom anchor also constraints

### Pinning to Edges of a view to another view

``` swift
extension UIView {
    func pinToEdges(of parentView: UIView) {
        topAnchor.constraint(equalTo: parentView.topAnchor).isActive = true
        leadingAnchor.constraint(equalTo: parentView.leadingAnchor).isActive = true
        trailingAnchor.constraint(equalTo: parentView.trailingAnchor).isActive = true
        bottomAnchor.constraint(equalTo: parentView.bottomAnchor).isActive = true
    }
}
```