
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

WelcomeAlert.performSegueToVerifyEnrollment(caller: self, params: faceSdkDict)
```
#### Handling the result

```swift
class  ViewController: UIViewController {
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
        faceStr = faceData?.getFaceImagePath() ?? ""

        print("EndUser: ErrorCode: ", errorCodeStr)
        print("EndUser: isFaceDetected: ", isFaceDetected)
        print("EndUser: isSmileDetected: ", isSmileDetected)
        print("EndUser: isEyeBlinkDetected: ", isEyeBlinkDetected)
        print("EndUser: isFaceStr: ", faceStr)
        
        if !(errorCodeStr == "901" || errorCodeStr == "906" || errorCodeStr == "905") {
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
| 801  | Scan timed out                |
| 802  | Invalid ID parameters passed|
| 803  | Camera permission denied    |
| 804  | Scan was interrupted            |
| 805  | Octus SDK License got expired             |
| 806  | Octus SDK License was invalid             |
| 807  | Invalid camera resolution   |
| 811  | QR not detected             |
| 812  | QR parsing failed           |
| 108  | Internet Unavailable                 |
| 401  | Api Limit Exceeded            |
| 429  | Too many request            |

## Forus Parameters

- `scanner.licenceKey = "LICENCE KEY"`   ***(Required)***
  
  Accepts the Octus licence key as a `String`
  
- `scanner.documentCountry = Country.countryISOcode.rawValue` ***(Required)***
  
  Sets the country associated with the Document.
  
  For the complete list of supported countries refer ``ISO_3166-1_alpha-2`` format code
 
- `scanner.documentType = Document.value.rawValue` ***(Required)***
  
  Sets the Document which has to be scanned. Possible values are, 
  
  | Value          | Effect                 |
  | -------------- | ---------------------- |
  | Document.PAN   | Pan Card               |
  | Document.ADR   | Aadhaar Card           |
  | Document.VID   | Voter ID               |
  | Document.NID   | National ID            |
  | Document.PPT   | Passport               |
  | Document.VSA   | Visa                   |
  | Document.DRV   | Driving Licence        |
  | Document.CQL   | Cheque Leaf            |
  | Document.GST   | GST Form               |
  | Document.IMG_A | Image Capture Aadhaar  |
  | Document.IMG_H | Plain Image capture    |
  
- `scanner.documentSubType = ScanMode.OCR.rawValue` ***(Required)***  
  
  Sets the Document Sub Type . Majority of the documents support only `ScanMode.OCR.rawValue` as a sub type. 
  
  *Documents where both `ScanMode.OCR.rawValue` and `ScanMode.BARCODE.rawValue` apply are ,*
  
  - `Document.ADR`
   
  *Documents where only `ScanMode.MRTD.rawValue` apply are ,*
 
  - `Document.PPT`
  - `Document.VSA`
  
  
  Possible values for Sub Type are,
  
  | Value            | Effect                         |
  | ---------------- | ------------------------------ |
  | ScanMode.OCR.rawValue | Scans the document in OCR mode |
  | ScanMode.BARCODE.rawValue | Scans the document in QR mode  |
  | ScanMode.MRTD.rawValue | Scans the document in MRZ mode  |
  | ScanMode.CROP.rawValue | Scans the document in Crop mode  |
  
## Help

For any queries/feedback , contact us at `support@frslabs.com` 

