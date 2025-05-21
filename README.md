
# FORUS iOS SDK

![version](https://img.shields.io/badge/version-v4.3.1-blue)

Forus SDK comes with a simple screen with multiple instructions to capture a perfect KYC compliant photograph. The SDK comes with active and passive liveness, compression, blur and exposure detection as standard.

Key features are,
- Face and liveness detection
- Anti-Spoof detection

You can find the release history at [Changelog](CHANGELOG.md)

‼ ATTENTION ‼ → BREAKING CHANGE introduced at Forus SDK `v4.1.1`. We have introduced a new license format. If you are using versions prior to `v4.1.1` and intend to update to `v4.1.1` or above please contact `support@frslabs.com` for an updated license.

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

- Xcode 13.0
- iOS 13.0+
- Swift 5.0

## Permission

In Info.plist file add following code to allow your application to access iPhone's camera:
``<key>NSCameraUsageDescription</key>
<string>Allow access to camera</string>``

Location access :
``<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>Allow access to location</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>Allow access to location</string>``

## Installation

#### CocoaPods
You can use [CocoaPods](http://cocoapods.org/) to install `Forus` by adding it to your `Podfile`:

```ruby
source 'https://gitlab.com/frslabs-public/ios/forus.git'
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '13.0'
target '<Your Target Name>' do
use_frameworks!
pod 'Forus', '4.3.1'
pod 'TensorFlowLiteSwift', '2.6.0'
end
```

###### Save/Edit Netrc settings to install custom pod

You will need a valid netrc credentials to install forus from maven, which can be obtained by contacting `support@frslabs.com`. 

1. Create or edit .netrc file under current user's home directory.
2. Write the below lines into that file, replace <YOUR_USERNAME> and <YOUR_PASSWORD> with your credentials which is shared through email and save the file.
```ruby
machine www.repo2.frslabs.space
login <YOUR_USERNAME>
password <YOUR_PASSOWRD>
```
3. In terminal enter below command to install the pod

"pod install" or "pod update" or "pod install --repo-update".

4. Connect with physical device to build and run Forus, It will not build/run in simulator due to camera dependency.

To get the full benefits import `Forus` wherever you import UIKit

``` swift
import UIKit
import Forus
```

## Usage example

```swift
import Forus

@objc func invokeForus(){
      * let forus = ForusController(showInstruction: true/false, delegate:self)     //If instruction page is needed should enable with "true" if not make it as "false"
      * forus.modalPresentationStyle = .fullScreen
      * forus.licenceKey = "YOUR-LICENCE_KEY"
      * forus.livenessMode = LivenessMode.smile.rawValue (smile) / LivenessMode.eyeBlink.rawValue (eyeblink) / LivenessMode.both.rawValue (smile and eyeblink)
        forus.setCamera = Utility.front.rawValue (default) / Utility.back.rawValue
        forus.callAntiSpoof = true / false (Boolean)
        forus.field1 = true / false (Boolean) //(field1 is optional field for time can be added to watermark on face image)
        forus.timeDuration = 8 (Int) //Dynamically maximum can set upto 60sec, Minimum time can be set to 8sec. If time is not needed then it should be 0 (no timeout option). Default it will be 0 sec.
        forus.timeFormat = "yyyy-MM-dd HH:mm:ss" (default) (String)
        forus.field2 = true / false (Boolean) //(field2 is optional field for location can be added to watermark on face image) 
        forus.field3 = "" (String) //(field3 is optional field with character limit 20 which can be added to watermark on face image) 
        forus.field4 = "" (String) //(field4 is optional field with character limit 20 which can be added to watermark on face image) 
        forus.field5 = "" (String) //(field5 is optional field with character limit 40 which can be added to watermark on face image) 
      * present(forus, animated: false, completion: nil)
}
```
#### Handling the result

```swift

class ViewController: UIViewController, ForusControllerDelegate {

    func forusControllerSuccess(_ scanner: ForusController, didFinishScanningWithResults results: forusResult) {
         print("Forus Result: ", results)
         let faceImage = results.faceResult   
         let isFaceDetected = results.isFaceDetected  
         let isSmileDetected = results.isSmileDetected  
         let isEyeBlinkDetected = results.isEyeBlinkDetected   
         let confidenceScore = results.confidence!   
         let isFaceReal = results.isFaceReal ?? Bool()
         let field1 = results.timeStamp   
         let field2 = results.location ?? String()
         let field3 = results.field3 ?? String()
         let field4 = results.field4 ?? String()
         let field5 = results.field5 ?? String()
    }
    
    func forusControllerDidCancel(_ scanner: ForusController) {
        
    }
    
    func forusControllerFailed(_ scanner: ForusController, didFailWithError error: Int) {
        print("Forus Error Code: ", error)
    }
    
}
``` 

## Forus Result

```swift

    let faceImage = results.faceResult   // Face image UIImage format 
    let isFaceDetected = results.isFaceDetected   // Boolean value for face detection
    let isSmileDetected = results.isSmileDetected   // Boolean value for smile detection on face
    let isEyeBlinkDetected = results.isEyeBlinkDetected   // Boolean value for eye blink detectin on face
    let confidenceScore = results.confidence!   //Confidence score for face detected
    let isFaceReal = results.isFaceReal ?? Bool()  //Returns "true" if face is real, returns "false" if face is fake.
    let field1 = results.timeStamp    // Timestamp is optional field which can be added to watermark on face image, Int64 format 
    let field2 = results.location  // Location is optional field which can be added to watermark on face image, String format
    let field3 = results.field3 // field3 is optional field which can be added to watermark on face image , String format
    let field4 = results.field4 // field4 is optional field which can be added to watermark on face image , String format
    let field5 = results.field5 // field5 is optional field which can be added to watermark on face image , String format
    
```     

## Forus Error Codes

Error codes and their meaning are tabulated below

| Code          | Message                 |
| -------------- | ---------------------- |
| 301  | Invalid settings  |
| 302  | Licence invalid   |
| 303  |  Licence expired  |
| 305  | Camera permission denied  |
| 307  | Face not detected  |
| 308  | Transaction failed / Unable to ping |
| 309  | Transaction limit exceeded                      |
| 310  | No internet connection                          |

## Forus Parameters

- forus.licenceKey = "LICENCE KEY" ***(Required)***

  - Accepts the Forus licence key as a String

- forus.livenessMode = LivenessMode.XXX  ***(Required)***

  - forus.livenessMode = LivenessMode.smile.rawValue 

    This mode offer liveness check through smile detection on face
  
  - forus.livenessMode = LivenessMode.eyeBlink.rawValue
  
    This mode offer liveness check through eyeblink detection on face
    
    
- forus.callAntiSpoof = true / false (Boolean)    ***(Required)***
    
  -  If antispoof is needed have to pass true boolean.
    
  -  If antispoof is not needed have to make it as false boolean.
    
 - forus.timeDuration = 8 (Int)  ***(Optional)***
 
   -  Default it is 0 sec if not set. 
     
   -  If need to set maximum limit is 60sec and minimum is 8sec.
     
   -  If time is not needed, no timeout option is there need to make it as 0sec.
       
 - forus.setCamera = Utility.front.rawValue (default) / Utility.back.rawValue 

    - If we need camera mode Front then input should be "forus.setCamera = Utility.front.rawValue"
    
    - If we need camera mode Back then input should be "forus.setCamera = Utility.back.rawValue"
      
  - forus.field1 = true / false (Boolean) ***(Optional)***
  
    - If we need to display the time on face image as watermark we need to pass true boolean.
    
    - If not needed need to pass false.
     
 - forus.timeFormat = "yyyy-MM-dd HH:mm:ss" (String) ***(Optional)***
     
    - We can pass time Format for timestamp on Face.
     
 - forus.field2 = true / false (Boolean) ***(Optional)*** 
    
    - If we need to display the location on face image as watermark we need to pass true boolean.
    
    - If not needed need to pass false.

 - forus.field3 = "" (String) ***(Optional)*** 
 
     -  If we need to display the field3 on face image as watermark we need to pass String value

     -  field3 has a character limit of 20.
     
 - forus.field4 = "" (String) ***(Optional)*** 
 
     -  If we need to display the field4 on face image as watermark we need to pass String value.
     
     -  field4 has a character limit of 20.
     
 - forus.field5 = "" (String) ***(Optional)*** 
 
     -  If we need to display the field5 on face image as watermark we need to pass String value.
     
     -  field5 has a character limit of 40.

 

     
  
  
## Help

For any queries/feedback , contact us at `support@frslabs.com` 

