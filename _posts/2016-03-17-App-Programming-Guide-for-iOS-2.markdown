---
layout: post
title:  "[Apple Dev]02.Expected App Behaviors"
date:   2016-03-17 09:00:00
categories: [iOS]
tags: [Translation,iOS,Application,Architecture,Apple]
---

# Expected App Behaviors

새로 생성되는 모든 Xcode의 프로젝트들은 iOS 시뮬레이턴, 실제 장치에서 실행이 되도록 설정이 됩니다. 하지만, 이렇게 간단히 장치에서 실행되는 것이 결코 App Store에 앱을 올릴 준비가 되었음을 뜻하지는 않습니다. 모든 앱은 유저에게 좋은 경험을 보장하기 위해서 많은 커스터마이징이 필요합니다. Customization 은 앱의 아이콘을 제공하는 것에서부터 정보를 어떻게 표현하고 사용할 건지에 대한 아키텍쳐-레벨의 결정까지 다양합니다. 이번 장에서는 모든 앱들이 다뤄지는 것과 개발자가 준비 과정중에 일찍 고려해야할 행동들을 설명합니다.

## Providing the Required Resources

개발자가 만드는 모든 앱들은 iOS 기기에 적절하게 표현되기 위해서 다음의 리소스셋과 메타데이터를 따라야합니다.
- An information property-list file : `Info.plist` 파일은 앱에 대한 메타데이터를 담고 있습니다. 이 메타데이터는 시스템이 앱과 상호작용하기 위해 사용됩니다. Xcode는 프로젝트의 환경설정값에 기초해서 자동적으로 생성이 됩니다. 프로젝트의 Info 탱ㅂ에서 이것을 직접 확인할 수 있으며, 내용을 수정할 수 있습니다. 이 파일을 편집하거나 포함되어야 할 권고사항들에 대해서는 밑에서 설명하겠습니다. (The Information Property List File 섹션)

- A declaration of the app’s required capabilities : 모든 앱들은 하드웨어의 사양이나 특성들을 실행되기 위해서 반드시 선언해야합니다. App Store는 이러한 정보를 바탕으로 특정 기기에서 유저들이 앱을 실핼할 수 있는지 없는지를 결정합니다. Info 탭의 Required device capabilities entry 에서 이러한 앱의 필수사항 리스트를 편집할 수 있습니다. 이 키값들을 어떻게 설정하는지에 대한 내용은 아래에서 설명됩니다.(Declaring the Required Device Capabilities)

- One or more icons. 시스템은 당신의 앱 아이콘을 홈화면에서 보여줍니다. 시스템은 세팅이나 검색 결과를 보여줄 떄, 다른 버전의 아이콘도 사용할 수 있습니다. 더 자세한 내용은 아래에서 설명하겠습니다. (App Icons)

- One or more launch images. 앱이 실행되면 시스템은 앱의 UI를 보여줄 떄 까지, 임시 이미지를 보여줍니다. 이 임시 이미지는 앱의 런치 이미지이며, 이것은 앱이 실행중이고, 곧 준비될거라는 즉각적인 피드백을 유저에게 줍니다. 개발자는 반드시 최소한 하나의 런칭 이미지를 준비하여야하며, 특정 시나리오에 대한 부가적인 런칭 이미지도 준비할 수 있습니다. 더 자세한 사항은 밑에서 설명됩니다. (App Launch (Default) Images)

이러한 리소스들은 모든 앱에 필요합니다. 많은 키들이 기본적으로 Info.plist에 들어가 있지는 않습니다. 대부분의 부가적인 키들은 특정 기능을 구현하려고 할때 필요합니다. 예를 들어, 마이크로폰을 사용하는 어떤 앱은 반드시 NSMicrophoneUsageDescription 키를 포함하여야하며, 앱이 그 기능을 어떻게 사용할 것인지에 대한 정보를 유저에게 제공하여야합니다.

### The App Bundle
iOS 앱을 빌드할 때, Xcode 는 그것을 bundle 로 패키지합니다. bundle 은 서로 관련있는 리소스들을 한 장소에 함께 모아놓은 파일 시스템의 디렉토리입니다. iOS app bundle은 실행가능한 앱의 파일과 앱 아이콘, 이미지 파일, localized content와 같은 supporting 리소스 파일을 가지고 있습니다. Table 1-1은 MyApp이라 불리우는 시연 목적의 전형적인 iOS app bundle을 보여주고 있습니다. 이 예는 단지 설명하기 위한 목적으로 사용됩니다. table 상의 몇몇 파일들은 개발하는 app bundle에 없을 수도 있습니다.

**Table 1-1** *A typical app bundle*
|File|Example|Description|
|-|-|-|
|App executable|MyApp|실행가능한 파일은 앱의 컴파일된 코드를 가지고 있습니다. 실행 파일의 이름은 앱 이름에 .app 확장자를 더한 것과 같습니다.<br/>**This file is required.**|
|The information property list file|Info.plist|이 Info.plist 파일은 앱의 설정값을 가지고 있습니다. 시스템은 이 데이터를 사용해서 앱과 어떻게 상호작용할지를 정합니다.<br/>**This file is required and must be called Info.plist.**|
|App icons|Icon.png<br/>Icon@2x.png<br/>Icon-Small.png<br/>Icon-Small@2x.png | 앱을 기기의 홈화면에서 표현하기 위해 사용됩니다. @2x가 붙은 것은 레티나 디스플레이 용도입니다.<br/>**An app icon is required**|
|Launch images|Default.png<br/>Default-Portrait.png<br/>Default-Landscape.png |시스템은 이 파일을 이용해서 앱이 실행되는 동안 임시 배경화면을 만듭니다. 앱이 앱의 UI를 보여줄 준비가 됐으면 이 배경화면은 지워집니다.<br/>**At least one launch image is required.**|
|Storyboard files (or nib files)|MainBoard.storyboard|스토리보드는 앱의 화면에 보여지는 뷰와 뷰컨트롤러를 가지고 있습니다. 스토리보드 안의 뷰들은 뷰컨트롤러에 의해서 구성이 됩니다. 스토리보드는 한 뷰들의 집합에서 다른 뷰들의 집합으로 유저들을 데려다주는 transitions(=segues)를 만들어줍니다.<br/>메인 스토리보드파일의 이름은 프로젝트를 생성할 때, Xcode에 의해서 만들어집니다. 물론 이 이름을 **Info.plist** 파일의 **UIMainStoryboardFile** 키 값을 변경함으로써 바꿀 수 있습니다. 스토리보드 대신에 **nib files** 를 사용하는 앱에서는 UIMainStoryboardFile 키 대신에 **NSMainNibFile** 키를 사용해서 메인 nib file을 설정할 수 있습니다.<br/>**The use of storyboards (or nib files) is optional but recommended.**|
|Ad hoc distribution icon| iTunesArtwork | ad hoc에 앱을 배포하려면, 512x512 픽셀 버전의 앱 아이콘을 포함해야합니다. 이 아이콘은 App Store에 의해서 보통 제공이 되지만, ad hoc 배포는 App Store에 올라가지 않으므로, app bundle 대신에 이 아이콘이 보여져야합니다.  iTunes는 이 아이콘을 사용해서 당신의 앱을 보여줍니다. <br/>** The filename of this icon must be iTunesArtwork and must not include a filename extension. ** <br/> **This file is required for ad hoc distribution but is optional otherwise. **|
|Settings bundle|Settings.bundle|Setting app을 통하여 커스텀 앱 preferences를 노출하기 원하면, 반드시  settings bundle을 포함하여야합니다. 이 bundle은 당신의 앱 preferences를 정의하기 위한 **property list** 데이터와 다른 리소스 파일들을 가지고 있습니다. Settings app은 이 정보를 사용해서 앱이 필요한 인터페이스 앨리먼트들을 조합합니다.<br/>**This bundle is optional.**|
|Nonlocalized resource files|sun.png<br/>mydata.plist|Nonlocalized된 리소스들은 이미지나 사운드 파일, 영화나 커스템 데이터를 포함하고 있습니다. 이러한 모든 파일들은 app bundle의 최상단에 위치하여야합니다.|
|Subdirectories for localized resources|en.lproj<br/>fr.lproj<br/>es.lproj| Localized된 리소스들은 반드시 language-specific 프로젝트 디렉토리 안에 있어야합니다. 이 디렉토리의 이름들은 "ISO 639-1 language" 형태의 축약형에 .lproj 접미사를 붙여서 표현됩니다. (예를 들어, en.lproj, fr.lproj, 와 es.lproj 디렉토리들은 영국과, 프랑스, 스페인에 특화된 리소스들을 포함하고 있습니다.)<br/>iOS앱은 internationalized 여야 하며, 지원하는 각 언어들을 위한 language.lproj 디렉토리를 가지고 있어야합니다. 앱의 커스텀 리소스의 localized 버전을 제공하는 것 뿐만 아니라, 앱 아이콘과 런칭 이미지, Settings icon을 localize할 수도 있습니다.|

더 자세한 정보는 다음의 링크를 참조하세요
[링크](https://developer.apple.com/library/ios/documentation/CoreFoundation/Conceptual/CFBundles/Introduction/Introduction.html#//apple_ref/doc/uid/10000123i)

### The Information Property List File
Xcode uses information from the General, Capabilities, and Info tabs of your project to generate an information property list (Info.plist) file for your app at compile time. The Info.plist file is a structured file that contains critical information about your app’s configuration. It is used by the App Store and by iOS to determine your app’s capabilities and to locate key resources. Every app must include this file.

Although the Info.plist file provided by Xcode includes default values for all of the required entries, most apps require some changes or additions. Whenever possible, use the General and Capabilities tabs to specify the configuration information for your app. Those tabs contain the most common configuration options available for apps. If you do not see a specific option on either of those tabs, use the Info tab.

For options where Xcode does not provide a custom configuration interface, you must provide appropriate keys and values. The Custom iOS Target Properties section of the Info tab contains a summary of the entries to be included in the Info.plist file. By default, Xcode displays human-readable descriptions of the intended feature but each feature actually corresponds to a unique key in the Info.plist file. Most keys are optional and used infrequently, but there are a handful of keys that you should consider when defining any new project:

- Declare your app’s required capabilities in the Info tab. The Required device capabilities section contains information about the device-level features that your app requires to run. The App Store uses the information in this entry to determine the capabilities of your app and to prevent it from being installed on devices that do not support features your app requires. For more information, see Declaring the Required Device Capabilities.
- Apps that require a persistent Wi-Fi connection must declare that fact. If your app talks to a server across the network, you can add the Application uses Wi-Fi entry to the Info tab of your project. This entry corresponds to the UIRequiresPersistentWiFi key in the Info.plist file. Setting this key to YES prevents iOS from closing the active Wi-Fi connection when it has been inactive for an extended period of time. This key is recommended for all apps that use the network to communicate with a server.
- Newsstand apps must declare themselves as such. Include the UINewsstandApp key to indicate that your app presents content from the Newsstand app.
- Apps that define custom document types must declare those types. Use the Document Types section of the Info tab to specify icons and UTI information for the document formats that you support. The system uses this information to identify apps capable of handling specific file types. For more information about adding document support to your app, see Document-Based App Programming Guide for iOS.
- Apps can declare any custom URL schemes they support. Use the URL Types section of the Info tab to specify the custom URL schemes that your app handles. Apps can use custom URL schemes to communicate with each other. For more information about how to implement support for this feature, see Using URL Schemes to Communicate with Apps.
- Apps should provide usage descriptions for certain app features. Whenever there is a privacy concern about an app accessing a user’s data or a device’s capabilities, iOS frameworks prompt the user and request permission for your app to use the feature. Apps that use these features should provide privacy usage descriptions that explain what your app plans to do with the corresponding data. For information about the features that require user permission, see Table 1-2.
- For detailed information about the keys and values you can include in the Info.plist file, see Information Property List Key Reference.

### Declaring the Required Device Capabilities
All apps must declare the device-specific capabilities they need to run. Xcode includes a Required device capabilities entry in your project’s Info tab and populates it with some minimum requirements. You can add values to this entry to specify additional requirements for your app. The Required device capabilities entry corresponds to the UIRequiredDeviceCapabilities key in your app’s Info.plist file.

The value of the UIRequiredDeviceCapabilities key is either an array or dictionary that contains additional keys identifying features your app requires (or specifically prohibits). If you specify the value of the key using an array, the presence of a key indicates that the feature is required; the absence of a key indicates that the feature is not required and that the app can run without it. If you specify a dictionary instead, each key in the dictionary must have a Boolean value that indicates whether the feature is required or prohibited. A value of true indicates the feature is required and a value of false indicates that the feature must not be present on the device. If a given capability is optional for your app, do not include the corresponding key in the dictionary.

For detailed information on the values you can include for the UIRequiredDeviceCapabilities key, see Information Property List Key Reference.

App Icons
Every app must provide an icon to be displayed on a device’s Home screen and in the App Store. An app may actually specify several different icons for use in different situations. For example, an app can provide a small icon to use when displaying search results and can provide a high-resolution icon for devices with Retina displays.

New Xcode projects include image asset entries for your app’s icon images. To add icons, assign the corresponding image files to the image assets of your project. At build time, Xcode adds the appropriate keys to your app’s Info.plist file and places the images in your app bundle.

For information about designing your app icons, including the sizes of those icons, see iOS Human Interface Guidelines.

### App Launch (Default) Images
When the system launches an app for the first time on a device, it temporarily displays a static launch image on the screen. This image is your app’s launch image and it is a resource that you specify in your Xcode project. Launch images provide the user with immediate feedback that your app has launched while giving your app time to prepare its initial user interface. When your app’s window is configured and ready to be displayed, the system swaps out the launch image for that window.

When a recent snapshot of your app’s user interface is available, the system prefers the use of that image over the use of your app’s launch images. The system takes a snapshot of your app’s user interface when your app transitions from the foreground to the background. When your app returns to the foreground, it uses that image instead of a launch image whenever possible. In cases where the user has killed your app or your app has not run for a long time, the system discards the snapshot and relies once again on your launch images.

New Xcode projects include image asset entries for your app’s launch images. To add launch images, add the corresponding image files to the image assets of your project. At build time, Xcode adds the appropriate keys to your app’s Info.plist file and places the images in your app bundle.

For information about designing your app’s launch images, including the sizes of those images, see iOS Human Interface Guidelines.

## Supporting User Privacy

Maintaining user privacy should be an important consideration when designing your app. Most iOS devices contain user and device data that users might not want to expose to apps or external entities. Remember that the user might delete your app if it uses data in an inappropriate way.

Access user or device data only with the user’s informed consent obtained in accordance with applicable law. In addition, take appropriate steps to protect user and device data and be transparent about how you use it. Here are some best practices that you can take:

Review guidelines from government or industry sources, including the following documents:
The Federal Trade Commission’s report on mobile privacy: Mobile Privacy Disclosures: Building Trust Through Transparency.
The EU Data Protection Commissioners’ Opinion on data protection for Mobile Apps: http://ec.europa.eu/justice/data-protection/article-29/documentation/opinion-recommendation/files/2013/wp202_en.pdf
The Japanese Ministry of Internal Affairs and Communications’ Smartphone Privacy Initiatives:
Smartphone Privacy Initiative (2012):
English: http://www.soumu.go.jp/main_sosiki/joho_tsusin/eng/presentation/pdf/Initiative.pdf

Japanese: http://www.soumu.go.jp/main_content/000171225.pdf

Smartphone Privacy Initiative II (2013):
English: http://www.soumu.go.jp/main_sosiki/joho_tsusin/eng/presentation/pdf/Summary_II.pdf

Japanese: http://www.soumu.go.jp/main_content/000247654.pdf

The California State Attorney General’s recommendations for mobile privacy: Privacy on the Go: Recommendations for the Mobile Ecosystem
These reports provide helpful recommendations for protecting user privacy. You should also review these documents with your company’s legal counsel.
Request access to user or device data that is protected by the iOS system authorization settings at the time the data is needed. Consider supplying a usage description string in your app’s Info.plist file explaining why your app needs that data. Data protected by iOS system authorization settings includes location data, contacts, calendar events, reminders, photos, and media; see Table 1-2. Provide reasonable fallback behavior in situations where the user does not grant access to the requested data.
Be transparent with users about how their data is going to be used. For example, you should specify a URL for your privacy policy or statement with your iTunes Connect metadata when you submit your app, and you might also want to summarize that policy in your app description.
For more information about providing your app’s privacy policy in iTunes Connect, see Adding an App in iTunes Connect.

Give the user control over their user or device data. Provide settings so that the user can disable access to certain types of sensitive information as needed.
Request and use the minimum amount of user or device data needed to accomplish a given task. Do not seek access to or collect data for non obvious reasons, for unnecessary reasons, or because you think it might be useful later.
Take reasonable steps to protect the user and device data that you collect in your apps. When storing such information locally, try to use the iOS data protection feature (described in Protecting Data Using On-Disk Encryption) to store it in an encrypted format. And try to use HTTPS when sending user or device data over the network.
If your app uses the ASIdentifierManager class, you must respect the value of its advertisingTrackingEnabled property. And if that property is set to NO by the user, then use the ASIdentifierManager class only for Limited Advertising Purposes. “Limited Advertising Purposes” means frequency capping, attribution, conversion events, estimating the number of unique users, advertising fraud detection, debugging for advertising purposes only, and other uses for advertising that may be permitted by Apple in Documentation for the Ad Support APIs.
If you have not already done so, stop using the unique device identifier (UDID) provided by the uniqueIdentifier property of the UIDevice class. That property was deprecated in iOS 5.0, and the App Store does not accept new apps or app updates that use that identifier. Instead, apps should use the identifierForVendor property of the UIDevice class or the advertisingIdentifier property of the ASIdentifierManager class, as appropriate.
If your app supports audio input, configure your audio session for recording only at the point where you actually plan to begin recording. Do not configure your audio session for recording at launch time if you do not plan to record right away. In iOS 7, the system alerts users when apps configure their audio session for recording and gives the user the option to disable recording for your app.
Table 1-2 lists the types of data authorizations supported by iOS. Using the services listed in this table causes an alert to be displayed to the user requesting permission to do so. You can determine if the user authorized your app for a service using the API listed for each item. You should view this table as a starting point for your app’s own privacy behaviors and not as a finite checklist. The contents of this table may evolve over time.

Table 1-2  Data protected by system authorization settings
Data
System authorization support
Location
The current authorization status for location data is available from the authorizationStatus class method of CLLocationManager. In requesting authorization in iOS 8 and later, you must use the requestWhenInUseAuthorization or requestAlwaysAuthorization method and include the NSLocationWhenInUseUsageDescription or NSLocationAlwaysUsageDescription key in your Info.plist file to indicate the level of authorization you require.
Photos
The authorization status for photo data is available from the authorizationStatus method of ALAssetsLibrary. To inform the user about how you intend to use this information, include the NSPhotoLibraryUsageDescription key in your Info.plist file.
Music, video, and other media assets
The authorization status for media assets is available from the authorizationStatus method of ALAssetsLibrary.
Contacts
The authorization status for contact data is available from the ABAddressBookGetAuthorizationStatus function. To inform the user about how you intend to use this information, include the NSContactsUsageDescription key in your Info.plist file.
Calendar data
The authorization status for calendar data is available from the authorizationStatusForEntityType: method of EKEventStore. To inform the user about how you intend to use this information, include the NSCalendarsUsageDescription key in your Info.plist file.
Reminders
The authorization status for reminder data is available from the authorizationStatusForEntityType: method of EKEventStore. To inform the user about how you intend to use this information, include the NSRemindersUsageDescription key in your Info.plist file.
Bluetooth peripherals
The authorization status for Bluetooth peripherals is available from the state property of CBCentralManager. To inform the user about how you intend to use Bluetooth, include the NSBluetoothPeripheralUsageDescription key in your Info.plist file.
Microphone
In iOS 7 and later, the authorization status for the microphone is available from the requestRecordPermission: method of AVAudioSession. To inform the user about how you intend to use the microphone, include the NSMicrophoneUsageDescription key in your Info.plist file.
Camera
In iOS 7 and later, the authorization status for the camera is available in deviceInputWithDevice:error: method of AVCaptureDeviceInput. To inform the user about how you intend to use the camera, include the NSCameraUsageDescription key in your Info.plist file.

## Internationalizing Your App

Because iOS apps are distributed in many countries, localizing your app’s content can help you reach many more customers. Users are much more likely to use an app when it is localized for their native language. When you factor your user-facing content into resource files, localizing that content is a relatively simple process.

Before you can localize your content, you must internationalize your app in order to facilitate the localization process. Internationalizing your app involves factoring out any user-facing content into localizable resource files and providing language-specific project (.lproj) directories for storing that content. It also means using appropriate technologies (such as date and number formatters) when working with language-specific and locale-specific content.

For a fully internationalized app, the localization process creates new sets of language-specific resource files for you to add to your project. A typical iOS app requires localized versions of the following types of resource files:

Storyboard files (or nib files)—Storyboards can contain text labels and other content that need to be localized. You might also want to adjust the position of interface items to accommodate changes in text length. (Similarly, nib files can contain text that needs to be localized or layout that needs to be updated.)
Strings files—Strings files (so named because of their .strings filename extension) contain localized versions of the static text that your app displays.
Image files—You should avoid localizing images unless the images contain culture-specific content. Whenever possible, you should avoid storing text directly in your image files. For images that you load and use from within your app, store text in a strings file and composite that text with your image-based content at runtime.
Video and audio files—You should avoid localizing multimedia files unless they contain language-specific or culture-specific content. For example, you would want to localize a video file that contained a voice-over track.
For information about the internationalization and localization process, see Internationalization and Localization Guide. For information about the proper way to use resource files in your app, see Resource Programming Guide.