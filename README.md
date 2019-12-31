
# Forus SDK

![version](https://img.shields.io/badge/pod-v1.0.0-red)

 Forus SDK comes with a simple screen with multiple instructions to capture a perfect KYC compliant photograph. The SDK comes with compression, blur and exposure detection as standard.

# Table Of Content
- [Features](#Features)
- [Prerequisite](#prerequisite)
- [Requirements](#requirements)
- [Permission](#Permission)
- [Installation](#installation)
- [Usage example](#Usage-example)
- [Octus Result](#Octus-Result)
- [Octus Parameters](#octus-parameters)
- [Octus Error Codes](#octus-error-codes)
- [Help](#help)

## Features

- [x] Auto scanning domestic and internationals ID cards
- [x] OCR is highly accurate 
- [x] Scan duration is very less 
- [x] MRTD document supported

## Prerequisite

You will need a valid license to use the Forus SDK, which can be obtained by contacting support@frslabs.com .

Depending on the license - offline or online - you have opted for, the ping functionality to billing servers will be disabled or enabled. For instance, if you have opted for the offline SDK model, then there will be no server ping needed to our billing server to bill you. However, if you have chosen a transaction based pricing, then after each transaction, a ping request will be made to our billing server. This cannot be overrided by the App. A point to note is that if the ping transaction fails for any reason, the whole transaction will be void without any results from the SDK.

Once you have the license , follow the below instructions for a successful integration of Forus SDK onto your IOS Application.

## Requirements

- iOS 10.0+
- Xcode 11.2

## Permission

In Info.plist file add following code to allow your application to access iPhone's camera:
``<key>NSCameraUsageDescription</key>
<string>Allow access to camera</string>``

## Installation

#### CocoaPods
You can use [CocoaPods](http://cocoapods.org/) to install `Octus` by adding it to your `Podfile`:

```ruby
platform :ios, '13.0'
use_frameworks!
pod 'Forus'
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
        var faceSdkDict = [String : Any]()
        let faceUtils = FaceUtility()
        faceSdkDict[faceUtils.SHOW_INSTRUCTION] = faceUtils.STATUS_NO
        faceSdkDict[faceUtils.CAMERA_MODE] = faceUtils.CAMERA_FRONT
        faceSdkDict[faceUtils.LICENCE_KEY] = "LICENCE KEY"
        faceSdkDict[faceUtils.SECURITY_LEVEL] = faceUtils.LEVEL_2 //It is for face and smile detection
        //faceUtils.LEVEL_0 --> It is for face detection
        //faceUtils.LEVEL_1 --> It is for face and eyeblink detection
       WelcomeAlert.performSegueToVerifyEnrollment(caller: self, params: faceSdkDict)
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

        print("EndUser: ErrorCode: ", errorCodeStr)
        print("EndUser: isFaceDetected: ", isFaceDetected)
        print("EndUser: isSmileDetected: ", isSmileDetected)
        print("EndUser: isEyeBlinkDetected: ", isEyeBlinkDetected)
        print("EndUser: isFaceStr: ", faceStr)
        
        if !(errorCodeStr == "901" || errorCodeStr == "902" || errorCodeStr == "903" || errorCodeStr == "904") {
            print("EndUser: get image")
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
     let faceImage : UIImage? = getImageFromDocDirectory()
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
| 000  | Sucess                |
| 901  | TimeOut |
| 902  | not detected eyeblink/smile    |
| 903  | Forus SDk Licence got expired            |
| 904  | Forus SDK License was invalid             |


## Forus Parameters

- faceSdkDict[faceUtils.LICENCE_KEY] = "LICENCE KEY" (Required)
Accepts the Forus licence key as a String
- faceSdkDict[faceUtils.CAMERA_MODE] = faceUtils.CAMERA_FRONT (Required)
It depends upon the users criteria to decide what they need camera front or camera back.
- faceSdkDict[faceUtils.SECURITY_LEVEL] = faceUtils.LEVEL_"code" (Required)
Level codes are required based on what the user wants to detect
  
## Help

For any queries/feedback , contact us at `support@frslabs.com` 

