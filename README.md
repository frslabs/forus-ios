
# FORUS iOS SDK

![version](https://img.shields.io/badge/version-v2.0.0-blue)

Forus SDK comes with a simple screen with multiple instructions to capture a perfect KYC compliant photograph. The SDK comes with compression, blur and exposure detection as standard.

You can find the release history at [Changelog](CHANGELOG.md)

# Table Of Content
- [Prerequisite](#prerequisite)
- [Requirements](#requirements)
- [Permission](#Permission)
- [Installation](#installation)
- [Usage example](#Usage-example)
- [Forus Result](#Forus-Result)
- [Forus Parameters](#Forus-parameters)
- [Forus Error Codes](#Forus-error-codes)
- [Help](#help)

## Prerequisites

You will need a valid license to use the Forus SDK, which can be obtained by contacting support@frslabs.com .

Depending on the license - offline or online - you have opted for, the ping functionality to billing servers will be disabled or enabled. For instance, if you have opted for the offline SDK model, then there will be no server ping needed to our billing server to bill you. However, if you have chosen a transaction based pricing, then after each transaction, a ping request will be made to our billing server. This cannot be overrided by the App. A point to note is that if the ping transaction fails for any reason, the whole transaction will be void without any results from the SDK.

Once you have the license, follow the below instructions for a successful integration of Forus SDK onto your IOS Application.

## Minimum Requirements

- iOS 11.0+
- Xcode 11.2

## Permission

In Info.plist file add following code to allow your application to access iPhone's camera:
``<key>NSCameraUsageDescription</key>
<string>Allow access to camera</string>``

## Installation

#### CocoaPods
You can use [CocoaPods](http://cocoapods.org/) to install `Forus` by adding it to your `Podfile`:

```ruby
source 'https://gitlab.com/frslabs-public/ios/forus.git'
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '11.0'
target '<Your Target Name>' do
use_frameworks!
pod 'Forus', '2.0.0'
end
```
To get the full benefits import `Forus` wherever you import UIKit

``` swift
import UIKit
import Forus
```

## Usage example

```swift
import Forus

@objc func callFaceSDK(){
     let forus = ForusController(delegate:self)
        forus.modalPresentationStyle = .fullScreen
        forus.licenceKey = "LICENCE_KEY"
        forus.livenessMode = LivenessMode.smile.rawValue (smile) / LivenessMode.eyeBlink.rawValue (eyeblink)
        present(forus, animated: false, completion: nil)
}
```
#### Handling the result

```swift

class ViewController: UIViewController, ForusControllerDelegate {

    func forusControllerSuccess(_ scanner: ForusController, didFinishScanningWithResults results: forusResult) {
        let faceImage = results.faceResult
        let isFaceDetected = results.isFaceDetected
        let isSmileDetected = results.isSmileDetected
        let isEyeBlinkDetected = results.isEyeBlinkDetected
        let timeStamp = results.timeStamp  
    }
    
    func forusControllerDidCancel(_ scanner: ForusController) {
        
    }
    
    func forusControllerFailed(_ scanner: ForusController, didFailWithError error: Int) {
        print("Forus Error Code -> ", error)
    }
    
    }
``` 

## Forus Error Codes

Error codes and their meaning are tabulated below

| Code          | Message                 |
| -------------- | ---------------------- |
| 901  | Camera permission denied  |
| 902 | Licence invalid |
| 903  |  Licence expired |
| 904  | Face not detected  |
| 905  | Liveness check failed  |
| 906  | Transaction failed / Unable to ping |

## Forus Parameters

- forus.licenceKey = "LICENCE KEY" (Required).

  Accepts the Forus licence key as a String

- forus.livenessMode = LivenessMode.smile.rawValue

  -- This mode offer liveness check through smile detection on face
  
  forus.livenessMode = LivenessMode.eyeBlink.rawValue
  
  -- This mode offer liveness check through eyeblink detection on face
  
  
  
## Help

For any queries/feedback , contact us at `support@frslabs.com` 

