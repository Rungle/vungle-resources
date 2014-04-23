# Vungle iOS Migration Guide

## Before You Begin...

This guide will show you how to upgrade to our newest SDK- version 3.0!

If you're new to Vungle, you'll want to use [this guide](https://github.com/Vungle/vungle-resources/blob/master/iOS-resources/iOS-SDK-dev-guide.md).

### Here are a few important tips:

* **This is a brand-new SDK!** As you go through this guide, you'll want to check **all** of your existing Vungle-related code. Our public methods have changed, but they will generally have an equivalent. 

* Our new SDK comes with a sample app, which is also available [here](https://github.com/Vungle/vungle-resources/tree/master/iOS-docs/iOS-sample-app). The Github version is missing our SDK, but a full copy is included in your SDK download.

* If you’re using one of the following development platforms, check out our plugins for updates: **Adobe Air**, **Unity-iOS**, **Unity-Android**, **Corona**, and **Marmalade**. 

Allright, let's upgrade!

## 1. Switch out the SDK

Remove vunglepub.embeddedframework from your project directory and from Xcode Frameworks.

Next, head **here** to download our new SDK. Unzip it.

## 2. Frameworks

### Add Our Framework

From the new SDK download, copy *VungleSDK.embeddedframework/* into your project directory, then drag and drop it into Xcode (*Frameworks*) to have it linked to your project.

### Add Additional Framework

The new SDK requires an additional framework that was not required in previous versions, so click on your project and head to:

*General > Linked Frameworks and Libraries*

Add the following if you haven't already:

* libsqlite3.dylib

It's also a good idea to check that the VungleSDK framework appears in there. If the drag-n-drop didn't link it automatically, then manually add it by clicking the '+' and then 'Add Other'.

## 3. Initialize the Vungle SDK

Find the code you've used to initialize our SDK and replace it with the following:

### AppDelegate.h:

`#import <VungleSDK/VungleSDK.h>`

### AppDelegate.m/didFinishLaunchingWithOptions:

```objc
NSString* appID = @"Your AppID Here";
VungleSDK* sdk = [VungleSDK sharedSDK];
// start vungle publisher library
[sdk startWithAppId:appID];
```

* The SDK no longer needs to be shut down, so you can remove [VGVunglePub stop] from your application delegate's applicationWillTerminate: method.

## 4. Play an Ad!

Nearly there! Now we just need to play an advert. Make sure you have the correct import statement in any header files. 

Then, anywhere you play a Vungle ad, you'll want to replace that code with:

```objc
VungleSDK* sdk = [VungleSDK sharedSDK];
[sdk playAd:self];
```

### Want to change the ads' default settings?

* **Options object:** To configure an ad's mute settings, orientation, and other customizeable options, you will now be creating and passing in a dictionary. See [line 70](https://github.com/Vungle/vungle-resources/blob/master/iOS-resources/iOS-sample-app/Vungle%20Sample%20App/FirstViewController.m) of the sample app for an example. The advanced settings below will cover the rest.

* **Incentivized ads:** This will also be an option in your dictionary. There are no longer separate methods to play incentivized and non-incentivized ads.

That's it! Migration guide complete. Stick around to learn more about customizing your ads!


## Advanced Settings

Check out our [reference](https://github.com/Vungle/vungle-resources/blob/master/iOS-docs/iOS-SDK-API-reference.md) for specific methods that you call to use these settings.

### Mute

The SDK has a property which you can set which will determine whether ads should start playing with sound. Note that the user can toggle sound once the ad unit has started.

### Incentivized Ads

You can choose to be notified whenever a user has completed an ad. A typical use case of this is when you are offering some sort of value exchange (‘watch this video and receive 100 gems!’). If you choose to make your ads incentivized, we’ll immediately send a message to your server along with a user id (that you provide) so that you can reward your users.

### Options

You can choose to pass in a dictionary into playAd which changes the default behaviour of the experience. We’ll list them here, but see the [reference](https://github.com/Vungle/vungle-resources/blob/master/iOS-docs/iOS-SDK-API-reference.md) for the specific keys and values that they should take. Each is optional to add to the dictionary, and if omitted they will take a default value. You need to pass in the options each time you show an ad, else the default settings will be used.

### Orientations

Determines in which orientation the video will show.

### Incentivized 

As mentioned above, you can determine whether this impression will send you a callback on video completion.

### userInfo

Used to pass us a username for the incentivized callback.

### showClose

A switch to determine whether you would like to give the user the option to skip out of the video. Note that this can be overridden by an option on our dashboard that will remove all skip buttons in your app (which will likely boost your performance).

### Events

You can implement the VungleSDK Delegate which can alert you to some useful events with regards to the ad experience. See the [reference](https://github.com/Vungle/vungle-resources/blob/master/iOS-docs/iOS-SDK-API-reference.md) for specific implementation details.

### willShowAd

This callback will be fired when the SDK is about to play an ad, so this is a useful place to have your game pause, mute, etc.

### willCloseAdWithViewInfo

At the end of our ad, there are two ways for the user to dismiss our unit: either by pressing the close button, or by clicking on the download button, in which case we will open the in-app app-store that iOS provides (using the StoreKit framework).

In both of these cases, this callback will get fired, as our ViewController exits. There is a boolean to alert you whether the ProductSheet will show. If the boolean is false, then this is the time to resume your app’s state. If it’s true, you’ll want to wait until the next callback fires.

There is also a viewInfo dictionary passed which contains some information about the user’s ad experience, which is useful if you’d like to provide client-side rewards.

### willCloseProductSheet

This final callback will fire in the case when a user had opted to download the advertised app, and is now closing out of the in-app app store. This is when you’ll want to resume the state of your app.
