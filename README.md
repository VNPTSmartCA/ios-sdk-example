# Example source code to integrate with VNPTSmartCA iOS SDK

To run the example project, clone repository, and run `pod install`

## Requirements

### Installation

VNPT SmartCA iOS SDK is available through [CocoaPods](https://cocoapods.org/). To install it, simply add the following line to your Podfile.

```ruby
pod 'VNPTSmartCAiOSSDK'
```

At a minimum, VNPT SmartCA iOS SDK is designed to work with iOS 9.0 or the newest.

### Configure Steps

***Step 1:*** Config file pList

```xml
<key>CFBundleURLTypes</key>
<array>
  <dict>
    <key>CFBundleURLName</key>
    <string></string>
    <key>CFBundleURLSchemes</key>
    <array>
      <string>partnerSchemeId</string>
    </array>
  </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>vnptsmartca</string>
  <string>vnptsmartcademo</string>
</array>
<key>NSAppTransportSecurity</key>
<dict>
  <key>NSAllowsArbitraryLoads</key>
  <true/>
</dict>
```

- ***CFBundleURLTypes***: Add scheme value. Note: *partnerSchemeId* provided by VNPT SmartCA. It's clientId.
- ***LSApplicationQueriesSchemes***: Add the scheme as *vnptsmartca* and *vnptsmartcademo*.

***Step 2:*** Import SDK in AppDelegate instance

```swift
import VNPTSmartCAiOSSDK // You need import before using

func application(_ application: UIApplication, open url: URL, sourceApplication: String?, annotation: Any) -> Bool {
    VNPTSmartCATransaction.receiveBackLink(url: url, sourceApp: sourceApplication!)
    return true
}

func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any]) -> Bool {
    VNPTSmartCATransaction.receiveBackLink(url: url, sourceApp: "")
    return true
}
```

*Note:* To iOS 13 or the newest, you need import SDK in SceneDelegate.swift file

```swift
import VNPTSmartCAiOSSDK // You need import before using

func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
    VNPTSmartCATransaction.receiveBackLink(url: URLContexts.first!.url, sourceApp: "")
}
```

***Step 3:*** Update layout and pass required param to open VNPT SmartCA App

*Note:*

- NotificationCenter registration VNPTSMARTCA NOTIFICATION KEYS should be removed when the ViewControllers relocating or dismissing completed
- Notification keys: `<VNPTSmartCA>NotificationCenterReceived`
- Before start open VNPT SmartCA app, you need specific environment to integrate by `VNPTSmartCATransaction.setEnvironment` function. Have two choices: `VNPTSmartCATransaction.ENVIRONMENT.DEMO` and `VNPTSmartCATransaction.ENVIRONMENT.PRODUCTION`.

```swift
import VNPTSmartCAiOSSDK // You need import before using

override func viewDidLoad() {
    //STEP 1: addObserver Notification
    NotificationCenter.default.removeObserver(self, name: NSNotification.Name(rawValue: "<VNPTSmartCA>NotificationCenterReceived"), object: nil)
    NotificationCenter.default.addObserver(self, selector: #selector(self.NotificationCenterTokenReceived), name:NSNotification.Name(rawValue: "<VNPTSmartCA>NotificationCenterReceived"), object: nil)
    
    //STEP 2: INIT TRANSACTION BASIC INFO AS CLIENT ID AND TRAN ID
    let tranInfo: NSMutableDictionary = NSMutableDictionary()

    tranInfo["clientId"] = "partnerSchemeId" // You need pass your client ID replace for 'partnerSchemeId'.
    tranInfo["tranId"] = "transactionId" // You need pass your transaction ID replace for 'transactionId'.
    
    VNPTSmartCATransaction.setEnvironment(_environment: VNPTSmartCATransaction.ENVIRONMENT.DEMO)
    VNPTSmartCATransaction.createTransactionInformation(info: tranInfo)
    
    //STEP 3: INIT LAYOUT - ADD BUTTON OPEN VNPTSMARTCA
    let buttonOpen = UIButton()
    buttonOpen.frame = CGRect(x: 20, y: 200, width: 260, height: 40)
    buttonOpen.setTitle("Open VNPT SmartCA", for: .normal)
    buttonOpen.setTitleColor(UIColor.white, for: .normal)
    buttonOpen.titleLabel!.font = UIFont.systemFont(ofSize: 15)
    buttonOpen.backgroundColor = UIColor.blue
    
    buttonOpen.addTarget(self, action: #selector(self.openApp), for: .touchUpInside)
    self.view.addSubview(buttonOpen)
}

@objc func openApp() {
    VNPTSmartCATransaction.handleOpen()
}
// You can listen return event from VNPTSmartCA app in here
@objc func NotificationCenterTokenReceived(notify: NSNotification) {
    let response: NSMutableDictionary = notify.object! as! NSMutableDictionary
        
    let _statusStr = "\(response["status"] as! String)"
    let _message = response["message"]
    
    if (_statusStr == "0") {
        print("Status code: \(_statusStr)")
        print("Message:", _message as! String)
    } else {
        
    }
}
```

***Request param***

| Param    | Description                                                                                           |
|----------|-------------------------------------------------------------------------------------------------------|
| tranId   | When you request create a signature transaction to VNPT SmartCA System by API, you'll receive tranId. |
| clientId | When you request integrate with VNPT SmartCA System. You'll receive a clientId through email.         |

***Response status code table***

| Code  | Description                                |
|-------|--------------------------------------------|
| 0     | Success                                    |
| 1     | User rejected                              |
| 2     | Unknown error                              |
| 3     | Device not found                           |
| 4     | Can not sign key challenge                 |
| 5     | PIN fail count                             |
| 6     | KAK Not found                              |
| 7     | PIN Not found                              |
| 8     | Token expired                              |
| 30000 | Client not found in system                 |
| 60000 | Credential not exist                       |
| 60001 | Credential not match identity              |
| 60002 | Credential no result                       |
| 60003 | Credential status invalid                  |
| 61000 | Credential assign key failed               |
| 62000 | Signature transaction not found            |
| 62001 | Signature transaction not match identity   |
| 62002 | Signature transaction expired              |
| 62003 | Signature transaction not waiting          |
| 62010 | Signature data request invalid format      |
| 63000 | Credential sign signer authen failed       |
| 63001 | Credential sign init hash signer failed    |
| 63002 | Credential sign file upload failed         |
| 64000 | Credential sign file not support file type |
| 64001 | Credential acceptance generate file failed |
| 64002 | Credential acceptance transaction exist    |

## Author

VNPT SmartCA Development Team

## Copyright ©

[Copyright (c) 2021 VNPTSmartCA](https://github.com/VNPTSmartCA/ios-sdk-example/blob/master/LICENSE).

## Contact - Support

email: hoangdinhoi@vnpt.vn
