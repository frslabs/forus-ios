
# FORUS iOS SDK

![version](https://img.shields.io/badge/version-v1.0.1-blue)

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

- iOS 10.0+
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
platform :ios, '12.0'
use_frameworks!
pod 'Forus', '1.0.1'
```

To get the full benefits import `Forus` wherever you import UIKit

``` swift
import UIKit
import FaceSDKFramework
```

## Usage example

```swift
import FaceSDKFramework
@objc func callFaceSDK(){
    var faceSdkDict = [String : Any]()
    let faceUtils = FaceUtility()
    faceSdkDict[faceUtils.LICENCE_KEY] = "LICENCE KEY"
    faceSdkDict[faceUtils.CAMERA_MODE] = faceUtils.CAMERA_FRONT    
    faceSdkDict[faceUtils.SECURITY_LEVEL] = faceUtils.LEVEL_Code // 1 or 2 or 3, Eg: "faceUtils.LEVEL_3"
    Forus.performSegueToVerifyEnrollment(caller: self, params: faceSdkDict)
}
```
#### Handling the result

```swift
class  ViewController: UIViewController {
   var faceData:FaceSDKResult? = nil
   var isFaceDetected = Bool()
   var isSmileDetected = Bool()
   var isEyeBlinkDetected = Bool()
   var errorCodeStr = String()
   
    override func viewDidLoad() {
        super.viewDidLoad()
        getResultSdk(data: "Data")
    }
    func getResultSdk(data : String) -> Void{
        NotificationCenter.default.addObserver(self, selector: #selector(getSIDResult(result:)), name: NSNotification.Name(rawValue: data), object: nil)
    }
    @objc func getSIDResult(result: NSNotification) {
        faceData = result.object as? FaceSDKResult
        isFaceDetected = faceData?.getFaceDetected() ?? false
        isSmileDetected = faceData?.getSmileDetected() ?? false
        isEyeBlinkDetected = faceData?.getEyeBlinkDetected() ?? false
        errorCodeStr = faceData?.getErrorCode() ?? ""
        
        if !(errorCodeStr == "901" || errorCodeStr == "902" || errorCodeStr == "903" || errorCodeStr == "904") {
            let faceImage : UIImage? = getImageFromDocDirectory()
            if faceImage != nil {
                faceImageView.image = faceImage
            }
        } 
    }

}
``` 

## Forus Result

```swift
     func getImageFromDocDirectory() -> UIImage{
        var faceImage = UIImage()
        let nsDocumentDirectory = FileManager.SearchPathDirectory.documentDirectory
        let nsUserDomainMask    = FileManager.SearchPathDomainMask.userDomainMask
        let paths               = NSSearchPathForDirectoriesInDomains(nsDocumentDirectory, nsUserDomainMask, true)
        if let dirPath  = paths.first {
            let imageURL = URL(fileURLWithPath: dirPath).appendingPathComponent("faceImage.png")
            let image = UIImage(contentsOfFile: imageURL.path)
            faceImage = image!
        }
        return faceImage
    }
```

## Forus Error Codes

Error codes and their meaning are tabulated below

| Code          | Message                 |
| -------------- | ---------------------- |
| 000  | Sucess  |
| 901  | Timeout |
| 902  | No eyeblink/smile detected  |
| 903  | Licence expired   |
| 904  | Invalid licence   |
| 905  | Transaction failed  |


## Forus Parameters

- faceSdkDict[faceUtils.LICENCE_KEY] = "LICENCE KEY" (Required).

  Accepts the Forus licence key as a String

- faceSdkDict[faceUtils.CAMERA_MODE] = faceUtils.CAMERA_FRONT (Required).

The user can choose to invoke either the front or the back camera depending on the flow of the App.
  - faceUtils.CAMERA_FRONT --> for front camera.
  - faceUtils.CAMERA_BACK --> for back camera.

- faceSdkDict[faceUtils.SECURITY_LEVEL] = faceUtils.LEVEL_Code (Required)

The user can configure the challenge levels needed for liveness check
  - faceUtils.LEVEL_1 --> It is for face detection.
  - faceUtils.LEVEL_2 --> It is for face and eyeblink detection.
  - faceUtils.LEVEL_3 --> It is for face and smile detection.
  - faceUtils.LEVEL_4 --> It is for randomaly selected eyeblink and smile detection.
  - faceUtils.LEVEL_5 --> It is for face and smile detection plus eye blink detection(Future release).
  
## Help

For any queries/feedback , contact us at `support@frslabs.com` 

