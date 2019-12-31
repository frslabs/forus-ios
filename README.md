# forus-ios
# FORUS SDK
![version](https://img.shields.io/badge/version-v2.0.0-blue)

Forus SDK comes with a simple screen with multiple instructions to capture a perfect KYC compliant photograph. The SDK comes with compression, blur and exposure detection as standard.

# Table Of Content

- [Prerequisite](#prerequisite)
- [Android SDK Requirements](#android-sdk-requirements)
- [Download](#download)
  - [Using maven repository](#using-maven-repository)
- [Setup](#setup)
  - [Permissions](#permissions)
  - [Proguard rules](#proguard-rules)
- [Quick Start](#quick-start)
  - [Initiating the Forus Sdk](#initiating-the-forus-sdk)
  - [Handling the result](#handling-the-result)
- [Forus Result](#forus-result)
- [Forus Error Codes](#forus-error-codes)
- [Forus Parameters](#forus-parameters)
- [Help](#help)

## Prerequisite

You will need a valid license to use the Forus SDK, which can be obtained by contacting `support@frslabs.com` . 

Depending on the license - offline or online - you have opted for, the ping functionality to billing servers will be disabled or enabled. For instance, if you have opted for the offline SDK model, then there will be no server ping needed to our billing server to bill you. However, if you have chosen a transaction based pricing, then after each transaction, a ping request will be made to our billing server. This cannot be overrided by the App. A point to note is that if the ping transaction fails for any reason, the whole transaction will be void without any results from the SDK.

Once you have the license , follow the below instructions for a successful integration of Forus SDK onto your Android Application.

## Android SDK Requirements

**Minimum SDK Version** -  **15**  and above

## Download

#### Using maven repository

Add the following code to your `project` level `build.gradle` file

```groovy
allprojects { 
    repositories { 
        
        //Maven credentials for the Forus SDK
        maven {
            url "https://forus-android.repo.frslabs.space/"
            credentials {
                   username '<YOUR_USERNAME>'
                   password '<YOUR_PASSOWRD>'
            }
        }
        
        /*
        *Include below code only for transaction based billing
        */
        //Maven credentials for the Torus SDK
        maven {
            url "https://torus-android.repo.frslabs.space/"
            credentials {
                username '<YOUR_USERNAME>'
                password '<YOUR_PASSOWRD>'
            }
        }
        
    }
}
```

After that, add the following code to your `app` level `build.gradle` file

```groovy
// ...

defaultConfig { 

    // ...
    
    ndk { 
        abiFilters "armeabi-v7a", "arm64-v8a", "x86", "x86_64" 
    } 

    vectorDrawables.useSupportLibrary true 
    
    renderscriptTargetApi 21
    renderscriptSupportModeEnabled false

}

// ...
```

And then, add the dependencies
```groovy

// ...

dependencies {
    /* Dependencies for Forus SDK */ 
    implementation 'com.android.support:design:<version above 23.4.0>'      
    implementation 'com.android.support.constraint:constraint-layout:<version above 1.1.3>'
   
    // Forus Core Dependency
    implementation('com.frslabs.android.sdk:forus:2.0.0')

    // Forus Additional Depedencies 
    implementation('com.google.android.gms:play-services-vision:17.0.2')
    
    // Optional - Required if transaction based billing is enabled
    // Forus billing dependencies
    implementation('com.frslabs.android.sdk:torus:0.0.9')
    implementation('com.squareup.retrofit2:converter-gson:2.3.0')
    implementation('com.squareup.retrofit2:retrofit:2.3.0')
    implementation('com.google.code.gson:gson:2.8.5')
}
```

## Setup

#### Permissions

Add the following permissions to your `AndroidManifest.xml` file

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="your.package.name" >

    <!-- Required by Forus -->
    <uses-permission android:name="android.permission.CAMERA" />

    <!-- Optional - Required if transaction based billing is enabled -->
    <uses-permission android:name="android.permission.INTERNET" />  
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

    <application>
      ...
    </application>

</manifest>
```

#### Proguard rules

Include below proguard rules only if transaction based billing is enabled

```java
-keep class retrofit.** { *; }

-keepclasseswithmembers class * {
   @retrofit2.http.* <methods>;
}
-keepclasseswithmembers interface * {
   @retrofit2.* <methods>;
}
```

## Quick Start

#### Initiating the Forus SDK

Initialize an instance of `Forus` with the appropriate parameters to invoke the Forus SDK

```
    Forus forus = new Forus.Builder()
          .setLicenceKey("<YOUR_LICENCE_KEY>")
          .setShowInstructions(true)
          .setCamera(Utility.CAMERA_FRONT)
          .setFaceCaptureTimeLimit(8)
          .setDetectEyeBlink(true)
          .setAutoAdjustExposure(true)
          .setCheckImageQuailty(true)
          .setImageQualityThreshold(Utility.MEDIUM)
          .setDetectSmile(false)
          .setNoTimeout(false)
          .setContext(<YOUR_ACTIVITY>.this)
          .setForusCallbackResponse(forusCallbackResponse)
          .build();
        
    forus.run();
```

#### Handling the result

You must implement `ForusCallbackResponse` to receive the result.

```
    ForusCallbackResponse forusCallbackResponse = new ForusCallbackResponse() {
        @Override
        public void onSuccess(ForusResult forusResult) {
            // Handle the result here
        }
        @Override
        public void onError(int code, String message) {
            // Handle the result here
        }
    };

```
For all `errorCode`'s and their meanings refer [Forus Error Codes](#forus-error-codes)

## Forus Result

You can use the following methods in the `ForusResult` instance to parse the success result:

| Return Type | Method                 | Usage                                      |
| ----------- | ---------------------- | ------------------------------------------ |
| String      | *getFaceImagePath()*   | JPEG File Path of face image               |
| boolean     | *isEyeBlinkDetected()* | true - If eye blink is detected            |
| boolean     | *isSmileDetected()*    | true - If smile is detected                |
| String      | *getLog()*             | Comma separated value of each frame status |

## Forus Error Codes

Following error codes will be returned on the `onError` method of the callback

| CODE | DESCRIPTION                  |
| ---- | ---------------------------- |
| 301  | Invalid settings             |
| 302  | Invalid licence              |
| 303  | Licence expired              |
| 304  | GMS dependency not available |
| 305  | Camera not available         |
| 306  | User canceled                |
| 307  | No face detected             |
| 308  | Unable to ping               |
| 309  | Transaction limit exceeded   |
| 310  | No internet connection       |

## Forus Parameters

See the below table for other options to initialize the settings

| Method                                               | Default              | Required | Comments                                                                                                                                                                 |
|:---------------------------------------------------- |:-------------------- |:-------- |:------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| *setLicenceKey(String* var1)                         | NULL                 | yes      |                                                                                                                                                                          |
| *setShowInstructions(boolean* var1)                  | false                | optional | If it is **true** then the instruction screen will be shown to the user before showing the face capture screen.                                                          |
| *setCamera(int* var1)                                | Utility.CAMERA_FRONT | optional | Utility.CAMERA_FRONT<br/>Utility.CAMERA_BACK <br>Utility.CAMERA_FRONT_OR_BACK                                                                                            |
| *setFaceCaptureTimeLimit(int var1)*                  | 8                    | optional | Integer value between 8 to 60 seconds.                                                                                                                                   |
| *setDetectEyeBlink(boolean var1)*                    | false                | optional | If it is **true** then the eye blink detection will be enabled.                                                                                                          |
| *setAutoAdjustExposure(boolean var1)*                | true                 | optional | If it is **false** then the SDK will not adjust the camera exposure level. Which will cause poor image quality or face will not be detected in poor lighting conditions. |
| *setCheckImageQuailty(boolean var1)*                 | true                 | optional | If it is **true** then the SDK will detect blurriness in the picture while capturing face and show a message to the user. Otherwise the face image quality may be poor.  |
| *setImageQualityThreshold(int* var1)                 | Utility.MEDIUM       | optional | Utility.LOW <br>Utility.MEDIUM <br>Utility.HIGH                                                                                                                          |
| *setDetectSmile(boolean var1)*                       | false                | optional | If it is **true** then the smile detection will be enabled.                                                                                                              |
| *setNoTimeout(boolean var1)*                         | false                | optional | If it is true then the SDK will override the *faceCaptureTimeLimit* to infinite.                                                                                         |
| *setContext(Activity var1)*                          | NULL                 | yes      | Pass your activity object.                                                                                                                                               |
| *setForusCallbackResponse(ForusCallbackResponse var1)* | NULL                 | yes      | Pass the ForusCallbackResponse object.                 


## Help
For any queries/feedback , contact us at `support@frslabs.com` 


