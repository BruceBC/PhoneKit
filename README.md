[![Carthage compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg?style=flat)](https://github.com/Carthage/Carthage)

# PhoneKit
Swift 4.0 framework for formatting and validating phone numbers.


## Installing with Carthage

[Carthage](https://github.com/Carthage/Carthage) is a decentralized dependency manager that automates the process of adding frameworks to your Cocoa application.

You can install Carthage with [Homebrew](http://brew.sh/) using the following command:

```bash
$ brew update
$ brew install carthage
```

To integrate PhoneKit into your Xcode project using Carthage, specify it in your `Cartfile`:

```ogdl
github "BruceBC/PhoneKit"
```

## Usage

Import PhoneKit at the top of the Swift file that will interact with a phone number.

```swift
import PhoneKit
```

Create a PhoneKit PhoneNumber enum
```swift
let phoneNumber: PhoneNumber = .elegant
```

Specify a direction
```swift
var typingDirection: PhoneNumberDirection = .forward
```

Format a phone number
```swift
phoneNumber.format(text: "216 123 4567", direction: typingDirection) // Result: (216) 123-4567
```

Validate a phone number
```swift
phoneNumber.validate(text: "216 123 4567") // Result: true
phoneNumber.validate(text: "216 123 456") // Result: false
```

PhoneNumber enum options
```swift
PhoneNumber.numbers.format(text: "216 123 4567", direction: typingDirection) // Result: 2161234567
PhoneNumber.elegant.format(text: "216 123 4567", direction: typingDirection) // (216) 123-4567
PhoneNumber.custom("[000] 0000-000").format(text: "216 123 4567", direction: typingDirection) // [216] 1234-567
```

PhoneNumber enum methods
```swift
phoneNumber.format(text: "216 123 4567", direction: typingDirection) // Result: (216) 123-4567
phoneNumber.validate(text: "216 123 4567") // Result: true
phoneNumber.raw(text: "(216) 123-4567") // Result: 2161234567
```

Example (Full example can be found in PhoneKitDemo.xcodeproj)
```swift
import UIKit
import PhoneKit

class ViewController: UIViewController {
    // MARK: - IBOutlets
    @IBOutlet weak var numbersTextField: PhoneTextField!
    @IBOutlet weak var elegantTextField: PhoneTextField!
    @IBOutlet weak var customTextField: PhoneTextField!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        setupTextFields()
    }
}

// MARK: - Setup
extension ViewController {
    func setupTextFields() {
        numbersTextField.phoneNumber = .numbers
        elegantTextField.phoneNumber = .elegant
        customTextField.phoneNumber = .custom("[216] - 8888-319")
    }
}

class PhoneTextField: UITextField, UITextFieldDelegate {
    var phoneNumber: PhoneNumber?
    var direction: PhoneNumberDirection = .forward
    
    
    required init?(coder aDecoder: NSCoder) {
        super.init(coder: aDecoder)
        
        self.delegate = self
        self.addTarget(self, action: #selector(textFieldDidChange(_:)), for: .editingChanged)
    }
    
    func textField(_ textField: UITextField, shouldChangeCharactersIn range: NSRange, replacementString string: String) -> Bool {
        if let char = string.cString(using: String.Encoding.utf8) {
            let isBackSpace = strcmp(char, "\\b")
            if (isBackSpace == -92) {
                direction = .backward
            } else {
                direction = .forward
            }
        }
        
        return true
    }
    
    @objc func textFieldDidChange(_ textField: UITextField) {
        guard let phoneNumber = phoneNumber else { return }
        textField.text = phoneNumber.format(text: textField.text ?? "", direction: direction)
        
        if validate(text: textField.text ?? "") {
            self.backgroundColor = #colorLiteral(red: 0.9019607843, green: 1, blue: 1, alpha: 1)
            self.layer.borderColor = #colorLiteral(red: 0, green: 0.6588235294, blue: 1, alpha: 1)
        } else {
            self.backgroundColor = #colorLiteral(red: 1, green: 0.8039215686, blue: 0.6431372549, alpha: 1)
            self.layer.borderColor = #colorLiteral(red: 0.9098039216, green: 0.2549019608, blue: 0.09411764706, alpha: 1)
        }
    }
    
    func validate(text: String) -> Bool {
        guard let phoneNumber = phoneNumber else { return false }
        return phoneNumber.validate(text: text)
    }
}
```

## Setup PhoneKitDemo.xcodeproj
```bash
$ git clone https://github.com/BruceBC/PhoneKit.git
$ cd ./PhoneKit/PhoneKitDemo
$ carthage update
```
