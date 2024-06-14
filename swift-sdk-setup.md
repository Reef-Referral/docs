---
layout: default
title: Swift SDK Setup
parent: SDK Setup
nav_enabled: true
---

# Swift iOS SDK Setup

----- 

## Requirements

The Reef Referral SDK is available in native Swift and supports iOS 13 and later. You can use SwiftUI or UIKit for integration. We're actively working on bringing support for React Native. If you have specific technical requirements, please reach out to us for assistance.

## Installation

The Reef Referral SDK is available for Swift through Swift Package Dependencies. You will receive access to the SDK on GitHub during the rollout phase.

## Setup

### Enable Deep Link support

Reef Referral relies on deep links to redirect users from the Offer Page to your app. In order for your app to be able to handle deep links:

1. Open your project in Xcode.
2. Select your app's Target.
3. Navigate to the Info tab.
4. In the URL Types section, click the "+" button to add a new URL Type.

![](/assets/images/74552c5-image.png)

In the form, fill in the following options:

1. Add new URL Type
2. `Identifier` – set it to your app's `Bundle ID`
3. `URL Schemes`– Retrieve this value from the Reef Referral Admin panel on the App settings tab.
4. `Role` – Select `Viewer`

### Initialise SDK

To initialise the SDK, call `start` function during your app startup. It's recommended to call this function inside `application:didFinishLaunchingWithOptions` for `UIKit`app or inside `App` constructor for SwiftUI apps. 

#### Swift UIKit
```swift 
import ReefReferral

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
      
      // Start Reef Referral SDK
			ReefReferral.shared.start(apiKey: <API_KEY>,  logLevel: .debug)
      
      return true
    }
}
```
#### SwiftUI
```swift 
@main
struct YourSwiftUIApp: App {
        
    init() {
        // Start Reef Referral SDK
        ReefReferral.shared.start(apiKey: API_KEY, logLevel: .debug)
    }
    var body: some Scene {
        WindowGroup {
            ContentView()
              .onOpenURL { url in
									// Handle opened deep link 
                  ReefReferral.shared.handleDeepLink(url: url)
              }
        }
    }
}

```

Replace `<API_KEY>` with the API key available on the App page in the Reef Referral dashboard. During development, it's recommended to use `logLevel: .debug`, and for production applications, switch to `.none`.

{: .note-title}
> 📘 Identifying users
> 
> If your app has an authentication system or other means of identifying users, consider using the `ReefReferral.shared.setUserId(<id>)` method to set the user ID. Otherwise, an anonymous ID will be generated.

### Handling  Deep Link

To notify the SDK that the link has been tapped, your app needs to call `ReefReferral.shared.handleDeepLink` method and pass in the URL. You can learn more about how deep links work on iOS in [Apple's documentation on the matter](https://developer.apple.com/documentation/xcode/defining-a-custom-url-scheme-for-your-app) . To configure the handling the deep link, see the code samples below. 

#### Swift UIKit (Scenes)
```swift 
class MainSceneDelegate: UIResponder, UIWindowSceneDelegate {
  
    func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
			// other scene initalization code
      
      ReefReferral.shared.handleDeepLink(connectionOptions: connectionOptions)
    }
  
    func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        ReefReferral.shared.handleDeepLink(URLContexts: URLContexts)
    }
}
```
#### Swift UIKit (Legacy)
```swift 
@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

    func application(_ application: UIApplication,
                     open url: URL,
                     options: [UIApplicationOpenURLOptionsKey : Any] = [:] ) -> Bool {

        ReefReferral.shared.handleDeepLink(url: url)

        return true
    }
}
```
#### SwiftUI
```swift 
@main
struct YourSwiftUIApp: App {
        
    init() {
        // Start Reef Referral SDK
        ReefReferral.shared.start(apiKey: API_KEY, logLevel: .debug)
    }
    var body: some Scene {
        WindowGroup {
            ContentView()
              .onOpenURL { url in
									// Handle opened deep link 
                  ReefReferral.shared.handleDeepLink(url: url)
              }
        }
    }
}
```

{: .warning-title}
> ❗️ Scene based UIKit apps
> 
> If your app opted into using [Scenes](https://developer.apple.com/documentation/uikit/app_and_environment/scenes),  make sure you implement the  `UIKit (Scenes)` variant, or the deep link will not be handled. To check if your app is using Scenes, check if your app implements `UIWindowSceneDelegate`and has `Application Scene Manifest` defined in `Info.plist`

{: .done-title}
> 👍 Setup completed
> 
> [Continue the tutorial to implement the referral.](/sdk-quickstart.html#31-adding-the-referral-sharing-screen-to-your-appp)
