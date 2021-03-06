# Usage
## Basics
### `.alert`
```swift
import ALRT

// Instantiate an .alert-type UIAlertController with OK and Cancel actions. Finally, present the alert by calling `show()`.
ALRT.create(.alert, title: "Title", message: "Message").addOK().addCancel().show()
```

### `.actionSheet`
```swift
// Instantiate an .actionSheet-type UIAlertController.
ALRT.create(.actionSheet, message: "Action Sheet")
    .addAction("Option A")
    .addAction("Option B")
    .addDestructive("Destructive Option")
    .show() 
```

## Action Types
Each action comes with different `UIAlertAction.Style`.

```swift
ALRT.create(.alert, title: "Action Types?")
    .addAction("🏂") // .default if not specified
    .addOK() // .default
    .addCancel("❌") // .cancel
    .addDestructive("💣") // .destructive
    .show()
```

## Custom Title
OK and Cancel actions have default titles in English; "OK" and "Cancel".
Here, we're overriding the titles in Japanese.

```swift
ALRT.create(.alert, title: "Actions In Japanese?").addOK("オーケー").addCancel("キャンセル").show()
```

## Action Handling
Each action has `handler` that is called when user taps the action.
The closure takes two parameters: `UIAlertAction` and `[UITextField]?`.
The former is self-explanatory.
The latter is present if text field(s) is/are added to the alert.

```swift
ALRT.create(.alert, title: "Action Handling")
    .addOK() { action, textFields in
        print("\(action.title!) tapped")
    }
    .show()
```

## Result Handling
`show()` has a completion handler that takes `Result`.
You can ensure if the alert was shown successfully or not. This is useful for unit tests.
```swift
ALRT.create(.alert, title: "Result Handling")
    .addOK()
    .show() { result in
        switch result {
        case .success:
            print("Alert is successfully shown")
        case .failure(let error):
            print("Error occurred. \(error.localizedDescription)")
        }
    }
```

## TextField(s)
Textfield(s) can be added to an alert in an use-case such as login.

```swift
enum TextFieldIdentifier: Int {
    case username
    case password
}

ALRT.create(.alert, title: "Enter your credentials")
    // Configure textfield
    .addTextField { textfield in
        textfield.placeholder = "Username"
        textfield.tag = TextFieldIdentifier.username.rawValue
    }
    .addTextField() { textField in
        textField.placeholder = "Password"
        textField.isSecureTextEntry = true
        textField.tag = TextFieldIdentifier.password.rawValue
    }
    // If an user selects "Login", textfields above are retrieved in the trailing closure. Distinguish one from another with a tag or identifier.
    .addAction("Login") { _, textfields in
        for textField in textfields ?? [] {
            if let identifier = TextFieldIdentifier(rawValue: textField.tag) {
                switch identifier {
                case .username:
                    // Extract username
                case .password:
                    // Extract password
                }
            }
        }
    }
    .addCancel()
    .show()
```

## Changing source ViewController to present from
Although ALRT can present an alert anywhere, you might want to specify a source view controller for some reason. This can be done easily by passing a view controller to `show()`.

```swift
ALRT.create(.alert, title: "Source?")
    .addOK()
    .show(self) // self = source view controller
```