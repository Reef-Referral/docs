---
layout: default
title: React Native Setup
nav_enabled: true
parent: SDK Setup
createdAt: "Mon Dec 18 2023 13:43:25 GMT+0000 (Coordinated Universal Time)"
updatedAt: "Fri Dec 22 2023 20:26:16 GMT+0000 (Coordinated Universal Time)"
---
# React Native SDK

-------- 

## Requirements

The Reef Referral React Native SDK supports iOS 13 and later, so you'll need to make sure to set the appropriate deployment target in your app.

{: .alert-title}
> 🚧 React Native SDK currently only supports iOS
> 
> We are working on bringing Android support in the future

## Installation

Install the package with the package manager of your choice

#### shell
```shell
pnpm install react-native-reef-referral # if you use pnpm
yarn add react-native-reef-referral # if you use yarn
npm i react-native-reef-referral # if you use npm
```

## Make sure `ReefReferral` Pod is installed

If you're using Expo SDK 49 or higher, make sure to install `expo-build-properties` (`npx expo install expo-build-properties` and configure it to install `ReefReferral` Pod by adding it to `plugins` in `app.json`. You need to be enrolled to Reef Referral to be able to download the Pod from repository.

#### app.json
```json 
{
  "expo": {
    // name, etc...
    "plugins": [
      [
        "expo-build-properties",
        {
          "ios": {
            "extraPods": [
              {
                "name": "ReefReferral",
                "git": "git@github.com:Reef-Referral/reef-referral-ios.git"
              }
            ]
          }
        }
      ]
    ]
  }
}
```

If you're using an older Expo SDK or bare React Native, modify `ios/Podfile` so it includes `ReefReferral`:

#### Podfile
```ruby 
target 'ReefReferralExample' do
  pod 'ReefReferral', git: 'git@github.com:Reef-Referral/reef-referral-ios.git'
  
  # use_react_native!, etc...
end
```

## Deep Linking

Reef Referral relies on deep links to redirect users from the Offer Page to your app. You will need to set up linking to your app to support that. [Linking to your app](https://docs.expo.dev/guides/linking/#linking-to-your-app) section of Expo Linking guide should instruct you how to set it up both in Expo and in bare React Native. If that fails, you can always modify the Xcode project manually as described in the [Swift iOS SDK Setup](/swift-sdk-setup#enable-deep-link-support).

## Setup

Import and call `useReefReferral()` to your root component.

#### App.tsx
```typescript 
import { useReefReferral } from "react-native-reef-referral";

export default function App() {
  useReefReferral({ apiKey: '<API_KEY>' });
  
  // Your app logic
}
```

Replace `<API_KEY>` with the API key available on the App page in the Reef Referral dashboard.

{: .note-title}
> 📘 Identifying users
> 
> If your app has an authentication system or other means of identifying users, consider using the `ReefReferral.setUserIdAsync(id)` method to set the user ID. Otherwise, an anonymous ID will be generated.

{: .done-title}
> 👍 Setup completed
> 
> [Continue the tutorial to implement the referral.](/sdk-quickstart.html#31-adding-the-referral-sharing-screen-to-your-appp)
