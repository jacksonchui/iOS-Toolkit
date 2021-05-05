# UIStackView

[`class UIStackView: UIView`](https://developer.apple.com/documentation/uikit/uistackview) is a container view that has properties for `distribution`, `alignment`, `spacing`, `axis` and more. Stack views take advantage of **intrinsic content sizing** when resizing content to fit their arrays.

For any view within a stack view, it should have a `var intrinsicContentSize: CGSize` set.

## Properties

### Axis

Options: `.horizontal` or `.vertical`.

### Alignment

* General Option: `fill` (fills available space perpendicular)
* Options for horizontal Stacks: `.top`, `.bottom`, `.firstBaseline`, `lastBaseline`
* Options for vertical Stacks: `.leading`, `.trailing`, `.center`

### Spacing

Set the spacing between elements within the stack view. Represents the minimum spacing for "Equal" distributions. Use negative values to overlap.

### Distribution

#### Fill

These distributions will fill the view if not full based of CH priority or make the subviews fit based on CR priority. If there is ambiguity, it will resize based off index in `arrangedSubviews`.

*.fill* will rearrange subviews so they fit the space along the axis.

**.fillEqually** makes all controls the same size, ignoring and **BREAKING** intrinsic content size to fill equally. If you add width or height constraint, Xcode will give you a purple ⚠️ noting the ambiguity.

**.fillProportionally** resizes views proportionally to fill all available space along a stack view's axis.

#### Equal

These distributions will shrink views based off compression resistance priority if they do not fit within the stack view. If there is ambiguity in CR priority, they will shrink based off index in the `arrangedSubviews` array.

1. **.equalSpacing** maintains equal spacing between each control. 
2. **.equalCentering** maintains equal spacing between the centers of controls.

UIStackView is a container

Not all distributions work the same

everything inside needs to be intrinsically content sized.

Stack Views want views to have an intrinsic content size.

Different distributions respect things differently


## Common Problems

### Making stack views scrollable: Adding `UIScrollView`

To add a scroll view, pin the stack view to the scroll view, and then pin the scroll view to the view that contains them.

```swift
func setupViews() {    
    let stackView = makeStackView(withOrientation: .vertical)
    let scrollView = makeScrollView()
    
    scrollView.addSubview(stackView)
    view.addSubview(scrollView)
    
    for i in 1...40 {
        let row = RowView()
        stackView.addArrangedSubview(row)
        
        // We handle padding on both sides with this Priority=1000 anchor
        row.widthAnchor.constraint(equalTo: scrollView.widthAnchor, multiplier: 1, constant: -32).isActive = true
    }
    
    stackView.pinToEdges(of: scrollView)
    
    // Pinning scrollview
    scrollView.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor).isActive = true
    scrollView.leadingAnchor.constraint(equalTo: view.leadingAnchor).isActive = true
    scrollView.trailingAnchor.constraint(equalTo: view.trailingAnchor).isActive = true
    scrollView.bottomAnchor.constraint(equalTo: view.safeAreaLayoutGuide.bottomAnchor).isActive = true
}
```

### Adding Margins/Padding to your stack view rows

Give a stackView layout margins as well as restrict the width. You need to restrict the width in order to get your row to have the right size. You need to add layout margins so within a row, the stackView knows how to position the row.


``` swift
let padding = 16
let spacing = 40
for i in 1...40 {
    let row = RowView()
    stackView.addArrangedSubview(row)
    
    // We handle padding on both sides with this Priority=1000 anchor
    row.widthAnchor.constraint(equalTo: scrollView.widthAnchor, multiplier: 1, constant: -padding*2).isActive = true
}

// for padding and spacing
stackView.isLayoutMarginsRelativeArrangement = true
stackView.directionalLayoutMargins = NSDirectionalEdgeInsets(top: spacing, leading: padding, bottom: spacing, trailing: padding)
```

### Keeping the size of a component as its intrinsic size ("Expanding Spotify Button")

By default, a `UIStackView` will hug the components inside of it based off their intrinsic size. When you **pin a UIStackView** to a container, it will resize all elements in it to fill the container.

To prevent `UIStackView` from stretching a component with an intrinsic size smaller than the area the StackView is pinned, the component should be embedded within a container, and the container embedded within the `UIStackView`.

For example, buttons have a tight hugging intrinsic content size around its `titleLabel` with `contentEdgeInsets`. To maintain its size in a `UIStackView` subview, it is wrapped.

```Swift
// buttons have a really tight hugging intrinsic content size. Putting it inside of a UIView allows us to center it.
// I think this is also possible by centering it to self instead of a container.
private func makeSpotifyButtonCustomView() -> UIView {
    let spotifyButton = makeSpotifyButton(withText: "PLAY ON SPOTIFY")
    
    let container = UIView()
    container.translatesAutoresizingMaskIntoConstraints = false
    container.heightAnchor.constraint(equalToConstant: buttonHeight).isActive = true
    container.addSubview(spotifyButton)
    
    spotifyButton.centerXAnchor.constraint(equalTo: container.centerXAnchor).isActive = true
    spotifyButton.centerYAnchor.constraint(equalTo: container.centerYAnchor).isActive = true
    spotifyButton.heightAnchor.constraint(equalToConstant: buttonHeight).isActive = true
    
    return container
}

func makeSpotifyButton(withText title: String) -> UIButton { ... }
```

### Make Rows go to edges of the Stack View

* Pinning StackView to the edges of the scrollView is not enough to overcome the intrinsic content size of a view. StackViews don't necessarily resize the view, even when pinned to the outer scrollView.

* Override the intrinsic content size of the row view once you have everything flush.

``` swift
func setupViews() {
        navigationItem.title = "Scrollable"
        
        let stackView = makeStackView(withOrientation: .vertical)
        let scrollView = makeScrollView()
        
        scrollView.addSubview(stackView)
        view.addSubview(scrollView)
        
        for _ in 1...40 {
            let row = RowView()
            stackView.addArrangedSubview(row)
            row.widthAnchor.constraint(equalTo: scrollView.widthAnchor).isActive = true // this is necessary. Cannot pin the stackView
        }
        
        // Pinning to the sides of view
        stackView.topAnchor.constraint(equalTo: scrollView.topAnchor).isActive = true
        stackView.leadingAnchor.constraint(equalTo: scrollView.leadingAnchor).isActive = true
        stackView.trailingAnchor.constraint(equalTo: scrollView.trailingAnchor).isActive = true
        stackView.bottomAnchor.constraint(equalTo: scrollView.bottomAnchor).isActive = true
        
        // Pinning scrollview
        scrollView.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor).isActive = true
        scrollView.leadingAnchor.constraint(equalTo: view.leadingAnchor).isActive = true
        scrollView.trailingAnchor.constraint(equalTo: view.trailingAnchor).isActive = true
        scrollView.bottomAnchor.constraint(equalTo: view.safeAreaLayoutGuide.bottomAnchor).isActive = true
    }
```

### My Custom View isn't behaving properly

* Give views an intrinsic content size so they always know how to size themselves.
* Lay them flush to their UIView container
* If necessary, pin the size of the view with constraints. That will override the intrinsic content size.

### My Custom View doesn't have a height or width

Try setting your intrinsic size with `UIView.noIntrinsicMetric`

```swift

class CustomView: UIView {
    ...
    override var intrinsicContentSize: CGSize {
        return CGSize(width: UIView.noIntrinsicMetric, height: 200)
    }
}
```