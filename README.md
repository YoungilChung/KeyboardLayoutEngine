KeyboardLayoutEngine
===

[![Version](https://img.shields.io/cocoapods/v/KeyboardLayoutEngine.svg?style=flat)](http://cocoapods.org/pods/KeyboardLayoutEngine)
[![License](https://img.shields.io/cocoapods/l/KeyboardLayoutEngine.svg?style=flat)](http://cocoapods.org/pods/KeyboardLayoutEngine)

⌨️ Simplest custom keyboard generator for iOS ever!  
  
![alt tag](https://raw.githubusercontent.com/cemolcay/KeyboardLayoutEngine/master/demo.gif)
  
`KeyboardLayoutEngine` is all about laying out keyboard buttons dynamically in a rectangle in a custom style easily but in fashion of original keyboard. For the sake of flexiblity, KeyboardLayoutEngine provides:

* `KeyboardLayout`: For laying out rows with custom paddings, colors.
* `KeyboardRow`: For laying out buttons or another set of KeyboardRow's inside.
* `KeyboardButton`: For rendering buttons in rows. Also provides flexible width, type and other very useful API's for flexiblty.
* They are also `UIView`s and handles their layout in their `layoutSubviews` function.
* They are faster than autolayout yet they can adopt perfectly any `CGFrame` you want apply a keyboard layout.
* That means they are play very well with orientation changes. (Layout for size class and/or orientation support is on the way.)
* `KeyboardLayoutStyle`, `KeyboardRowStyle` and `KeyboardButtonStyle` structs handles pretty much everything about styling.
* `KeyboardLayoutDelegate` for inform about button presses.  
* Also `CustomKeyboard` provided out of box, a good start point for figuring out how it works other than being of fully functional original keyboard.  

Install
----
#### CocoaPods

``` ruby
use_frameworks!
# Target Keyboard
pod 'KeyboardLayoutEngine'
```

Usage
----

* Describe your keyboard with custom styles, rows and buttons with either text or image in it.  
* Checkout the [CustomKeyboardLayout](https://github.com/cemolcay/KeyboardLayoutEngine/blob/master/Keyboard/CustomKeyboard/CustomKeyboardLayout.swift) for detailed usage.

``` swift
let keyboardLayout = KeyboardLayout(
  style: CustomKeyboardLayoutStyle,
  rows: [
    KeyboardRow(
      style: CustomKeyboardRowStyle,
      characters: [
        KeyboardButton(type: .Key("Q"), style: CustomKeyboardKeyButtonStyle),
        KeyboardButton(type: .Key("W"), style: CustomKeyboardKeyButtonStyle),
        KeyboardButton(type: .Key("E"), style: CustomKeyboardKeyButtonStyle),
        KeyboardButton(type: .Key("R"), style: CustomKeyboardKeyButtonStyle),
        KeyboardButton(type: .Key("T"), style: CustomKeyboardKeyButtonStyle),
        KeyboardButton(type: .Key("Y"), style: CustomKeyboardKeyButtonStyle),
        KeyboardButton(type: .Key("U"), style: CustomKeyboardKeyButtonStyle),
        KeyboardButton(type: .Key("I"), style: CustomKeyboardKeyButtonStyle),
        KeyboardButton(type: .Key("O"), style: CustomKeyboardKeyButtonStyle),
        KeyboardButton(type: .Key("P"), style: CustomKeyboardKeyButtonStyle),
      ]
    )
  ]
)

override func viewDidLoad() {
	super.viewDidLoad()
	view.addSubview(keyboardLayout)
}

override func viewDidLayoutSubviews() {
	super.viewDidLayoutSubviews()
	keyboardLayout.setNeedsLayout()
}
```

KeyboardLayoutDelegate
----

* Implement `KeyboardLayoutDelegate` for get information about the button presses.

``` swift
@objc public protocol KeyboardLayoutDelegate {
  optional func keyboardLayoutDidStartPressingButton(keyboardLayout: KeyboardLayout, keyboardButton: KeyboardButton)
  optional func keyboardLayoutDidPressButton(keyboardLayout: KeyboardLayout, keyboardButton: KeyboardButton)
}
```

KeyboardButtonWidth
----

``` swift
public enum KeyboardButtonWidth {
  case Dynamic
  case Static(width: CGFloat)
  case Relative(percent: CGFloat)
}
```

* Laying out buttons in rows are important. Since rows can their child rows, calculating right sizes for buttons and rows done by button types.  
* If you leave `.Dynamic` which is default by the way, every button in a row, it will calculate their width by `KeyboardRowStyle.buttonPadding` and total width of row and figure out equal widths with equal buttonPaddings.  
* Static will be static width obviusly.  
* Relative is an interesting one, which takes a value between [0, 1], fills percent of parent row, smartly calculated.

KeyboardButtonType
----

``` swift
public enum KeyboardButtonType {
  case Key(String)
  case Text(String)
  case Image(UIImage?)
}
```

* A button can be `Key`, `Text` or `Image`.  
* Key case might be useful for `textDocumentProxy.insertText`operation.
* Text case might be useful for buttons like "space", "return", "ABC", "123" or any string include emojis.
* Image case might be useful for buttons like "shift", "backspace", "switch keyboard" etc.

Styling
----

* Every style struct has their default values in taste of original keyboard.  
* If you dont assign a value in `init` function of a style struct, it will be loaded with its default value.  

KeyboardLayoutStyle
----
Definition:

``` swift
public struct KeyboardLayoutStyle {
  public var topPadding: CGFloat
  public var bottomPadding: CGFloat
  public var rowPadding: CGFloat
  public var backgroundColor: UIColor
}
```

Example:

``` swift
let CustomKeyboardLayoutStyle = KeyboardLayoutStyle(
  topPadding: 10,
  bottomPadding: 5,
  rowPadding: 13,
  backgroundColor: UIColor(red: 208.0/255.0, green: 213.0/255.0, blue: 219.0/255.0, alpha: 1))
```

KeyboardRowStyle
----

Definition:


``` swift
public struct KeyboardRowStyle {
  public var leadingPadding: CGFloat
  public var trailingPadding: CGFloat
  public var buttonsPadding: CGFloat
}
```

Example:

``` swift
let CustomKeyboardRowStyle = KeyboardRowStyle(
  leadingPadding: 5,
  trailingPadding: 5,
  buttonsPadding: 6)
```

KeyboardButtonStyle
----

Definition:

``` swift
public struct KeyboardButtonStyle {
  public var backgroundColor: UIColor
  public var cornerRadius: CGFloat

  // Border
  public var borderColor: UIColor
  public var borderWidth: CGFloat

  // Shadow
  public var shadowColor: UIColor
  public var shadowOpacity: Float
  public var shadowOffset: CGSize
  public var shadowRadius: CGFloat
  public var shadowPath: UIBezierPath?

  // Text
  public var textColor: UIColor
  public var font: UIFont

  // Image
  public var imageSize: CGFloat?

  // Popup
  public var showsPopup: Bool
  public var popupWidthMultiplier: CGFloat
  public var popupHeightMultiplier: CGFloat
}
```

Example:

``` swift
let CustomKeyboardDarkImageButtonStyle = KeyboardButtonStyle(
  backgroundColor: UIColor(red: 180.0/255.0, green: 188.0/255.0, blue: 201.0/255.0, alpha: 1),
  imageSize: 18,
  showsPopup: false)
```

CustomKeyboard
----

Default iOS Keyboard implementation with `KeyboardLayoutEngine`.  

* Shift toggle mechanism  
* Backspace mechanisim  
* Key button popups  
* `textDocumentProxy` integrations with `CustomKeyboardDelegate`  
* Ridiculusly easy implementation in `KeyboardViewController`  
* Change default styles before initilze it and you have your fully functional custumised standard English QWERTY keyboard!

``` swift
override func viewDidLoad() {
    super.viewDidLoad()
    CustomKeyboardLayoutStyle.backgroundColor = UIColor.redColor()
    CustomKeyboardRowStyle.buttonsPadding = 5
    customKeyboard = CustomKeyboard()
    customKeyboard.delegate = self
    view.addSubview(customKeyboard)
}
```

#### CustomKeyboard styles

* CustomKeyboardLayoutStyle: `KeyboardLayoutStyle`
* CustomKeyboardRowStyle: `KeyboardRowStyle`
* CustomKeyboardSecondRowStyle: `KeyboardRowStyle`
* CustomKeyboardChildRowStyle: `KeyboardRowStyle`
* CustomKeyboardSpaceButtonStyle: `KeyboardButtonStyle`
* CustomKeyboardBackspaceButtonStyle: `KeyboardButtonStyle`
* CustomKeyboardShiftButtonStyle: `KeyboardButtonStyle`
* CustomKeyboardGlobeButtonStyle: `KeyboardButtonStyle`
* CustomKeyboardReturnButtonStyle: `KeyboardButtonStyle`
* CustomKeyboardNumbersButtonStyle: `KeyboardButtonStyle`
* CustomKeyboardKeyButtonStyle: `KeyboardButtonStyle`

CustomKeyboardDelegate
----

* Provides information about key and special button presses.  

``` swift
@objc public protocol CustomKeyboardDelegate {
  optional func customKeyboardDidPressKeyButton(customKeyboard: CustomKeyboard, key: String)
  optional func customKeyboardDidPressSpaceButton(customKeyboard: CustomKeyboard)
  optional func customKeyboardDidPressBackspaceButton(customKeyboard: CustomKeyboard)
  optional func customKeyboardDidPressGlobeButton(customKeyboard: CustomKeyboard)
  optional func customKeyboardDidPressReturnButton(customKeyboard: CustomKeyboard)
}
```
