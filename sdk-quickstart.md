---
layout: default
title: SDK Quickstart
nav_enabled: true
nav_order: 1
---

# SDK Quickstart
This guide you will walk you through how to add referral system to your subscription app 

-------

# 1. What is Reef Referral?

Reef Referral is a platform designed to simplify the setup and management of referral systems for mobile subscription apps. With Reef Referral, you can effortlessly establish a referral system with just a few lines of code, empowering your users to become advocates who promote your app within their networks. While we currently support iOS apps, we are actively working to expand our platform's reach to Android and web platforms.

To get a clearer understanding of the referral process from a user's perspective, refer to How Reef Referral works

## 🎁Reward Options

Reef Referral offers two primary reward options for your referral campaigns:

### Standard Setup – App Store Offer Codes (Recommended):

Using App Store Offer Codes simplifies implementation and reduces the chances of errors. Additionally, when the offer has been used, your users will continue with the full price unless they cancel the subscription.

You can select one of the three options for a discount:

* Pay as you go – your customers pay a discounted price for each billing period for the selected duration. For example, $1.99 per month for the first 3 months
* Pay up front – your customers pay a discounted price for a selected duration. For example, $1.99 for the first 2 months.
* Free – you can allow your subscribers to access the subscription for free for the selected duration

AppStore Offer Codes also allow customisation of the eligibility of the referral:

* New – Users who have never subscribed
* Existing – Users who are currently subscribed
* Expired – Users who were previously subscribed but had their subscription expire

[You can read about the offer codes here.](https://developer.apple.com/help/app-store-connect/manage-subscriptions/set-up-offer-codes/)

### Manual Setup:

This option provides more flexibility, but it requires your app to handle the logic for granting rewards. Reef Referral will notify your app about the referral's status and reward eligibility, leaving the reward distribution in your hands. Additional details on the manual setup can be found in the "Additional Options" section.

# 2. 🔑 Signing up

To create your account, sign up at [https://www.reefreferral.com/sign-up](https://www.reefreferral.com/sign-up)

# 3. 📱App Setup

To setup your app in Reef Referral:

1. Visit the Reef Referral dashboard at [https://app.reefreferral.com](https://app.reefreferral.com).
2. Create a New App by clicking "New App" in the top-right corner.

   ![App creation screen](/assets/images/create_app.png)
3. Provide the app's name, App Store / Apple ID (found in App Information on App Store Connect), and an app icon URL.
4. If you opt for the Standard mode, set the "App Store Shared Secret" required for decoding the App Store Receipt and automatically marking rewards as granted.

## 2.1 Generate Reward Offer Codes

To generate reward offer codes, follow these steps in your App Store Connect:

1. Select your app in the App Store Connect.
1. Navigate to Subscriptions > Subscription Group > Subscription > View All Subscription Pricing > Offer Codes. You can find more details on generating offer codes in [Apple's documentation](https://developer.apple.com/help/app-store-connect/manage-subscriptions/set-up-offer-codes/).
1. Generate two custom offer codes — one for sender (referring user) rewards and the other for receiver (referred user) rewards.
1. Create a Custom Offer Code for each of the Offer Codes.

![Generate Offer Code](/assets/images/generate_offer_code.png)


## 2.2 Reward Offer Setup

Configure your reward offer settings:

1. Enter the name of the offer code (exactly matching the App Store).
1. Provide the custom code on the Offer page.
1. Specify the number of redeemed referrals required to unlock the sender reward in the Eligibility section

![Copy generate offer code](/assets/images/copy_offer_code.png)
![Offer code setup](/assets/images/configure_offer_code.png)

{: .note-title}
> 📘Enable Automatic Redirection
>
>We recommend enabling Automatic Redirection to the App Store, unless you've implemented a custom receiver UI in your app. With this option enabled, users will automatically be redirected to the App Store to accept the receiver offer when they tap the "Redeem" button on the Coupon page.

## 2.3 Coupon Page Customisation

The Coupon Page is displayed when the user taps on the referral link. See [How Reef Referral works](/). On the Page tab you can customise the text displayed on the Coupon Page to include specifics about your offer. You will also see the live preview of how the page looks.

![Coupong page setup](/assets/images/coupon_page_customisation.png)

{: .alert-title}
> 🚧 Test Mode
> 
> If your app is in Test Mode (see App settings), the page tab will display a yellow "test" label. Test Mode disables some protection functions to facilitate testing, such as allowing users to refer themselves.




# 3.📦 SDK Setup

Now that you've set up your app on the Reef Referral dashboard, it's time to integrate the Reef Referral SDK into your iOS app. This chapter will guide you through the process.

The SDK setup will depend on the technology that your are using to build your app:

1. [Swift iOS SDK Setup](swift-sdk-setup.html)
1. [React Native SDK](react-native-sdk-setup.html)
1. Android Kotlin (coming soon)

## 3.1 Adding the Referral Sharing Screen to Your Appp

To access all the relevant referral states simply call getReferralStatus:

#### Swift
```swift 
// concurency
let status = try? await ReefReferral.shared.getReferralStatus()
// access properties of the `status` object


// callback
ReefReferral.shared.getReferralStatus { result in
		if let status = try? result.get() {
      // access properties of the `info` object
    }
}

```

#### React Native
```js
import * as ReefReferral from "react-native-reef-referral";

export default function App() {
  const { referralStatus: status } = ReefReferral.useReferralStatus();
}
```


The `status` object offers referral-related information using the following properties:

## 3.2 Sender properties

Use the status object to design a sender screen.

`status.senderStatus` contains all relevant information about for the state of referral on the sender side, like the . The sender (or a referrer) is the person who is sharing the link, so use these properties to display a sharing UI and grant sharing rewards.

`status.senderStatus.linkURL` – current user's referral link

`status.senderStatus.redeemedCount` – number of redeemed offers from this link

`status.senderStatus.rewardEligibility` – eligibility for the sender reward

`status.senderStatus.offerCodeURL` – sender reward offer code redemption URL

Here is an example to implement the sender offer screen:

#### SwifUI
```swift 
// `status` object comes from ReefReferral.shared.getReferralStatus()

switch status.senderStatus.rewardEligibility {
  case .not_eligible:
    Text("Link redeemed \(status.senderStatus.redeemedCount) times")
  
 		if let link = status.senderStatus.linkURL { 
        	ShareLink("Share this link with more friends to unlock the reward", item: link)
    }
 case .eligible:
    Text("You reward is ready")
    Button("Claim yout reward"){ 
        	if let rewardLink = status.senderStatus.offerCodeURL { 
            	UIApplication.shared.open(rewardLink))
				  }
    }
  case .redeemed:
    Text("You have already redeemed your referral reward")
}

```

#### React Native
```typescript 
import {
  Text,
  Button,
  Linking,
  Share,
} from 'react-native';
import * as ReefReferral from 'react-native-reef-referral';

export function ReferralSection() {
  const { referralStatus: status } = ReefReferral.useReferralStatus();

  if (!status) {
    return null;
  }

  switch (status.senderStatus.rewardEligibility) {
    case ReefReferral.SenderRewardStatus.NotEligible:
      return (
        <>
          <Text>Link redeemed {status.senderStatus.redeemedCount} times</Text>

          {status.senderStatus.linkUrl && (
            <Button
              onPress={() =>
                Share.share({
                  url: status.senderStatus.linkUrl!,
                })
              }
              title="Share this link with more friends to unlock the reward"
            />
          )}
        </>
      );

    case ReefReferral.SenderRewardStatus.Eligible:
      return (
        <>
          <Text>Your reward is ready</Text>

          {status.senderStatus.offerCodeUrl && (
            <Button
              onPress={() => Linking.openURL(status.senderStatus.offerCodeUrl!)}
              title="Claim your reward"
            />
          )}
        </>
      );

    case ReefReferral.SenderRewardStatus.Redeemed:
      return <Text>You have already redeemed your referral reward</Text>;
  }
}
```

The offer code URL will redirect the user to the AppStore and prompt them to accept the referral reward. If you are not using Offer Codes as the rewards, see the "Manual Mode" below. 

If using Offer Codes, we will automatically mark the user as redeemed when we detect that the Offer Code has been claimed. 

{: .note}
>  Use `UIActivityViewController` or `ShareLink` (in SwiftUI) to share the `linkURL`

{: .note}
>  Use `UIApplication.shared.open(offerCodeURL)` to present the offer code

{: .done-title}
> 👍 That's it! That's all what's needed to implement referral in your app
> 
> There are additional customisation options below, if your referral program requires more flexible setup.

# 4 📃 Additional options

## 4.1 Accessing receiver reward status

By default, the receiving users will be automatically redirected to the AppStore to receive their reward and then back to the app.

However, if you would like to introduce additional logic before granting the reward, you can disable `Enable Automatic Redirection` option on the `Offer` page of the Reef Referral dashboard. This is useful for example when you want to limit the reward redemption to a segment of users, or add some additional UI elements. 

 In that case, use `status.receiverStatus` to access information about the state and the reward for the receiving user and you will need to manually open the Offer Code URL to prompt users to accept the reward. 

`status.receverStatus.rewardEligibility`– describes if the current user is eligible for the reward or if they already claimed the reward. The user becomes eligible after opening the referral link that is then handled by `handleDeepLink` 

`status.receiverStatus.offerCodeURL` – the AppStore Offer Code URL that unlocks the receiver reward in the Standard mode. Opening this URL will open the AppStore app and show a prompt asking the user to accept the discounted offer. 

## 4.2 Manual Mode

You can also manually grant the rewards, not using the offer code system. This is useful when your reward system is more complex, for example if there are multiple rewards based on the number of referred users, or your rewards are not granted using AppStore Offer Codes. 

To use the manual mode, check when the sender or receiver status becomes `eligible` and start your logic responsible for granting the referral. After the reward is granted, use the following methods to notify Reef Referral so that the status of the user can be updated.   

`ReefReferral.shared.triggerSenderSuccess`– when the sender (or referrer) reward has been granted by your app. 

`ReefReferral.shared.triggerSenderSuccess`– when the receiver (or referred user) reward has been granted by your app.

## 4.3 Reacting to status changes

If you would like to receive updates on the status of the referrals, set the `ReefReferralDelegate` object (in Swift) or use the `useReferralStatus()` hook (in React Native):

#### Swift
```swift
ReefReferral.shared.delegate = self
```
The delegate contains a single method, returning a result anytime the status has been changed

#### Swift
```swift
class YourClass: ReefReferralDelegate {
  func statusUpdated(referralStatus: ReefReferral.ReferralStatus) {
    // access referralStatus
  }
}
```

#### React Native
```typescript
import { useReferralStatus } from "react-native-reef-referral";

export function ReferralStatusIndicator() {
  const { referralStatus } = useReferralStatus();
  
  // ..
}
```



# 5. ✉️ Contact and support

If you have any questions or need further assistance, don't hesitate to contact us at [support@reefreferral.com](mailto:support@reefreferral.com)
