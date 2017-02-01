TUNE SDK Bridge
=============
The **TUNE SDK Bridge** provides a React Native interface to the native TUNE SDK methods for
attribution, measuring app events and In-App Marketing on both **iOS** and **Android**.

## Example Usage

```javascript

import TuneSDK from 'react-native-tune-sdk';

const params = {
  id           : '0001',
  userIdType   : 'facebook',
  email        : 'someone@example.com',
  name         : 'Jane Smith',
  age          : 50,
  gender       : 'FEMALE',
  location     : { latitude : 120.999, longitude : 90.000,  description : '' }
};

TuneSDK.login( params );

```

## Table of Contents
 - [Installation](#installation)
  - [Installation with rnpm](#installation-with-rnpm)
  - [iOS Manual Installation](#ios-manual-installation)
  - [Android Manual Installation](#android-manual-installation)
 - [Javascript API](#javascript-api)
  - [Authentication](#authentication)
    - [Login](#login)
    - [Registration](#registration)
  - [eCommerce](#ecommerce)
    - [Add to Cart](#add-to-cart)
    - [Add to Wishlist](#add-to-wishlist)
    - [Added Payment Info](#added-payment-info)
    - [Checkout Initiated](#checkout-initiated)
    - [Purchase](#purchase)
    - [Reservation](#reservation)
  - [Content](#content)
    - [Content View](#content-view)
    - [Search](#search)
    - [Reservation](#reservation)
  - [Gaming](#gaming)
    - [Achievement Unlocked](#achievement-unlocked)
    - [Level Achieved](#level-achieved)
    - [Spent Credits](#spent-credits)
    - [Tutorial Complete](#tutorial-complete)
  - [Sharing](#sharing)
    - [Invite](#invite)
    - [Rated](#rated)
    - [Share](#share)
  - [Custom User Profile Variables](#custom-user-profile-variables)   
    - [Register Custom Profile String](#register-custom-profile-string)
    - [Register Custom Profile Date Time](#register-custom-profile-date-time)
    - [Register Custom Profile Number](#register-custom-profile-number)
    - [Register Custom Profile Geo location](#register-custom-profile-geolocation)
    - [Set Custom Profile String Value](#set-custom-profile-string-value)
    - [Set Custom Profile DateTime Value](#set-custom-profile-date-time-value)
    - [Set Custom Profile Number Value](#set-custom-profile-number-value)
    - [Set Custom Profile Geo location Value](#set-custom-profile-geolocation-value)
    - [Clear Custom Profile Variable](#clear-custom-profile-variable)
    - [Clear All Custom Profile Variables](#clear-all-custom-profile-variables)
 - [In App Marketing Features](#in-app-marketing-features)
  - [Enabling Push](#enabling-push)
  - [Power Hooks Registration](#power-hooks-registration)
  - [Deep Actions](#deep-actions)


## Installation

### Installation with npm and react-native link

1. `npm install --save react-native-tune-sdk`
2. `react-native link`

*(note: this project hasn't been published to npm yet. Until then, use `npm install --save TuneOSS/react-native-tune-sdk`)*

[react-native link](http://facebook.github.io/react-native/releases/0.40/docs/linking-libraries-ios.html) will link most of the dependencies for the TUNE SDK Bridge for you, but you will still manually install the TUNE SDK core framework and perform a few linking and code modification steps manually as listed below.

This package installs the TUNE SDK Bridge for React Native. This exports bindings to the TUNE SDK methods so you can access the native SDK methods from the javascript you write in your React Native app.

Please refer to the [TUNE SDK iOS Quick Start](https://developers.tune.com/sdk/ios-quick-start/) and the [TUNE SDK Android Quick Start](https://developers.tune.com/sdk/android-quick-start/) for instructions on installing and linking the core framework in your app.


## iOS Bridge Installation
You can ignore steps 2, 3 and 4 if you ran `react-native link`. These steps are automatically completed for you during the linking phase.

1. `npm install --save react-native-tune-sdk`
2. In Xcode, right-click the Libraries folder under your project ➜ `Add Files to <your project>`.
3. Go to `node_modules` ➜ `react-native-tune-sdk` ➜ `ios` ➜ `RNTuneSDKBridge` and add the `RNTuneSDKBridge.xcodeproj` file.
4. Add libRNTuneSDKBridge.a from the linked project to your project properties ➜ "Build Phases" ➜ "Link Binary With Libraries"
5. Next you will have to link a few more SDK framework/libraries which are required (if you do not already have them linked.) Under the same "Link Binary With Libraries", click the + and add the following:
  - AdSupport.framework
  - CoreSpotlight.framework
  - CoreTelephony.framework
  - iAd.framework
  - libz.tbd or libz.dylib
  - MobileCoreServices.framework
  - QuartzCore.framework
  - Security.framework
  - StoreKit.framework
  - SystemConfiguration.framework
  - UserNotifications.framework (only on Xcode 8 to support iOS 10+)

6. Under your project properties ➜ "Info", add a new line with the following for you TUNE SDK config:
  * Create a dictionary named TUNE.
  * Add a string with a key of advertiserId and a value of your advertiser Id.
  * Add a string with a key of conversionKey and a value of your conversion Key.

  ![alt tag](https://shashinno.s3.amazonaws.com/tune/dictionary.shot.tune.ids.png)

7. In your `AppDelegate.m` import `Tune` (above the `@implementation AppDelegate` line)

```obective-c
@import Tune;
@implementation AppDelegate
```

8. In your `AppDelegate.m` add the code below to the bottom of the lifecycle method `didFinishLaunchingWithOptions` (before the `return YES`).

```objective-c
// TUNE SDK Initialize
NSDictionary *tuneConfig = [[NSBundle mainBundle] objectForInfoDictionaryKey:@"Tune"];
[Tune initializeWithTuneAdvertiserId:[tuneConfig objectForKey:@"advertiserId"] tuneConversionKey:[tuneConfig objectForKey:@"conversionKey"]];
```

9. Lastly, add to the `AppDelegate.m` in the applicationDidBecomeActive lifecycle method the Tune `[Tune measureSession]` messure session call like below:


```objective-c
- (void)applicationDidBecomeActive:(UIApplication *)application
{
  // Attribution will not function without the measureSession call included
  [Tune measureSession];
}
```

## Android Manual Installation

# Prerequisites for Android

Consult [this guide](https://developer.android.com/sdk/installing/adding-packages.html) if you are unsure how to do this. Specifically step 3 for the mentioned packages.


Steps Up steps:
  * npm install react-native-tune-sdk
  * Add Tune package to the android/setting.gradle file
  * Add Tune package to your android/app/build.gradle
  * Add and initialize TUNE SDK in MainActivity.java
  * Create a Java class file next to MainActivity file and create a class to initial the TUNE SDK.
  * Add the class you created from the above step to AndroidManifest.xml and add permissions.

1. `npm install --save react-native-tune-sdk`

2. Add the following in `android/setting.gradle`

  ```gradle
  ...
  include ':TuneSDKBridge', ':app'
  project(':TuneSDKBridge').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-tune-sdk/android')
  ```

3. And the following in `android/app/build.gradle`

  ```gradle
  ...
  dependencies {
      ...
      compile project(':TuneSDKBridge')
  }
  ```

4. Register package in `MainActivity.java`


  ** React Native 0.16+ and above.

  ```java
  // Step 1; import package:
  import com.tune.react.TuneSDKBridge.TuneSDKBridgePackage; // <!----

  public class MainActivity extends Activity implements DefaultHardwareBackBtnHandler {
      ...

    @Override
    protected List<ReactPackage> getPackages() {
      return Arrays.<ReactPackage>asList(
        new MainReactPackage(),
        // INITIALIZE TRACKING APP WITH YOUR advertiser id, conversion id, and Google api IMA sender id ( optional )
        new TuneSDKBridgePackage(),
        );
    }
      ...

  ```

 5. Create the TuneSDK class to initialize the TUNE SDK.

   ```java

	package com.name.of.your.package;

	import com.tune.Tune;
	import com.tune.ma.application.TuneApplication;

	public class TuneSDK extends TuneApplication {

	    private static Tune tuneInstance;
	    private static final String tuneAdvertiserId = "your_advertisment_id";
	    private static final String tuneConversionKey = "your_conversion_id";

	    @Override
	    public void onCreate() {
	        super.onCreate();
	        tuneInstance = Tune.init( this, tuneAdvertiserId, tuneConversionKey);
	    }

	}

   ```

6. Add the class new TuneSDK class to your AndroidManifest.xml application section and above that, add the permissions below.

```xml

    <!--  Need permissions -->
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
    <!-- Add the application -->
    <application
        android:name=".TuneSDK"
        android:allowBackup="true"
        android:icon="@mipmap/hasoffers"
        android:label="@string/app_name"
        android:theme="@style/AppTheme">

```  

  React Native Versions 0.16 < and lower

  ```java
  // Step 1; import package:
  import com.tune.react.TuneSDKBridge.TuneSDKBridgePackage; // <!----

  public class MainActivity extends Activity implements DefaultHardwareBackBtnHandler {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        ...

        mReactInstanceManager = ReactInstanceManager.builder()
            .setApplication(getApplication())
            .setBundleAssetName("index.android.bundle")
            .setJSMainModuleName("index.android")
            .addPackage(new MainReactPackage())
            // INITIALIZE
            .addPackage(new TuneSDKBridgePackage()
            .setUseDeveloperSupport(BuildConfig.DEBUG)
            .setInitialLifecycleState(LifecycleState.RESUMED)
            .build();

          ...
       }   


  ```  

# Javascript API


## Authentication


### Login


See the [TUNE SDK docs](http://developers.mobileapptracking.com/mobile-sdks-app-events-auth-login/) for more info

Example :

```js

    const params = {
        id           : '0001',
        userIdType   : 'facebook',
        email        : 'someone@example.com',
        name         : 'Jane Smith',
        age          : 50,
        gender       : 'FEMALE',
        location     : { latitude : 120.999, longitude : 90.000,  description : '' }
    };

    TuneSDK.login( params );

```    

* **id (required):** String - id of the customer
* **userIdType (required):** String - the user id type, must be one of these facebook, twitter, google or user if you are using a non-third party id.
* **email (required):** String - user email address
* **name:** String - name of the user
* **age :** Integer - user age
* **gender (required):** String - MALE or FEMALE all upper case.
* **location:** Object
  * **longitude:** Float - latitude
  * **longitude:** Float - longitude
  * **description** String - description or Street address of the location



### Registration


See the [TUNE SDK docs](https://developers.mobileapptracking.com/mobile-sdks-app-events-auth-registration/) for more info

Example :

```js

   const params = {
        id           : '0001',
        userIdType   : 'facebook',
        email        : 'someone@example.com',
        name         : 'Jane Smith',
        age          : 50,
        gender       : 'FEMALE',
        location     : { latitude : 120.999, longitude : 90.000,  description : '' }   
   };

   TuneSDK.registration( params );

```

* **id (required):** String - id of the customer
* **userIdType (required):** String - the user id type, must be one of these facebook, twitter, google or user if you are using a non-third party id.
* **email (required):** String - user email address
* **name:** String - name of the user
* **age :** Integer - user age
* **gender (required):** String - MALE or FEMALE all upper case.
* **location:** Object
  * **longitude:** Float - latitude
  * **longitude:** Float - longitude
  * **description** String - description or Street address of the location


##eCommerce



### Add To Cart


See the [TUNE SDK docs](https://developers.mobileapptracking.com/mobile-sdks-app-events-ecom-add-to-cart/) for more info

Example :

```js

    const params = {
        id           : '0001',
        userIdType   : 'facebook',
        gender       : 'MALE',
        age          : 50,
        revenue      : 0.0,
        currencyCode : 'USD',
        location     : { latitude : 120.999, longitude : 90.000,  description : '' },
        eventItems   : [{itemName : 'book', unitPrice : 1.00, quantity : 1, revenue : 0.0, attribute1 : '',attribute2 : '',attribute3 : '',attribute4 : '',attribute5 : '' }]    
    };

    TuneSDK.addToCart( params );

```

* **id (required):** String - id of the customer
* **userIdType (required):** String - the user id type, must be one of these facebook, twitter, google or user if you are using a non-third party id.
* **gender (required):** String - MALE or FEMALE all upper case.
* **age :** Integer - user age
* **revenue :** Float - the revenue
* **currencyCode:** String - the currency code of the transaction exp. 'USD'
* **location:** Object
  * **longitude:** Float - latitude
  * **longitude:** Float - longitude
  * **description** String - description or Street address of the location
* **eventItems:** Array of Objects
  * **event:** Object - example object
    * **itemName:** String - item name
    * **unitPrice:** - Float - unit price of item added to cart
    * **quantity:** - Integer - the quantity number of the item added to cart
    * **revenue:** - Float - the revenue
    * **attribute1:** - String
    * **attribute2:** - String
    * **attribute3:** - String
    * **attribute4:** - String
    * **attribute5:** - String



### Add To Wishlist


See the [TUNE SDK docs](https://developers.mobileapptracking.com/mobile-sdks-app-events-ecom-add-to-wishlist/) for more info

Example :

```js

    const params = {
        id           : '0001',
        userIdType   : 'facebook',
        currencyCode : 'USD',
        location     : { latitude : 120.999, longitude : 90.000,  description : '' },
        eventItems   : [{itemName : 'book', unitPrice : 1.00, quantity : 1, revenue : 0.0, attribute1 : '',attribute2 : '',attribute3 : '',attribute4 : '',attribute5 : '' }]    
    };

    TuneSDK.addToWishList( params );

```

* **id (required):** String - id of the customer
* **userIdType (required):** String - the user id type, must be one of these facebook, twitter, google or user if you are using a non-third party id.
* **currencyCode:** String - the currency code of the transaction exp. 'USD'
* **location:** Object
  * **longitude:** Float - latitude
  * **longitude:** Float - longitude
  * **description** String - description or Street address of the location
* **eventItems:** Array of Objects
  * **event:** Object - example object
    * **itemName:** String - item name
    * **unitPrice:** - Float - unit price of item added to cart
    * **quantity:** - Integer - the quantity number of the item added to cart
    * **revenue:** - Float - the revenue
    * **attribute1:** - String
    * **attribute2:** - String
    * **attribute3:** - String
    * **attribute4:** - String
    * **attribute5:** - String



### Added Payment Info


See the [TUNE SDK docs](https://developers.mobileapptracking.com/mobile-sdks-app-events-ecom-added-payment-info/) for more info

Example :

```js

    const params = {
        id           : '0001',
        userIdType   : 'facebook',
        currencyCode : 'USD',
        location     : { latitude : 120.999, longitude : 90.000,  description : '' },
        eventItems   : [{itemName : 'book', unitPrice : 1.00, quantity : 1, revenue : 0.0, attribute1 : '',attribute2 : '',attribute3 : '',attribute4 : '',attribute5 : '' }]    
    };

    TuneSDK.addedPaymentInfo( params );

```

* **id (required):** String - id of the customer
* **userIdType (required):** String - the user id type, must be one of these facebook, twitter, google or user if you are using a non-third party id.
* **currencyCode:** String - the currency code of the transaction exp. 'USD'
* **location:** Object
  * **longitude:** Float - latitude
  * **longitude:** Float - longitude
  * **description** String - description or Street address of the location
* **eventItems:** Array of Objects
  * **event:** Object - example object
    * **itemName:** String - item name
    * **unitPrice:** - Float - unit price of item added to cart
    * **quantity:** - Integer - the quantity number of the item added to cart
    * **revenue:** - Float - the revenue
    * **attribute1:** - String
    * **attribute2:** - String
    * **attribute3:** - String
    * **attribute4:** - String
    * **attribute5:** - String



### Checkout Initiated


See the [TUNE SDK docs](https://developers.mobileapptracking.com/mobile-sdks-app-events-ecom-checkout/) for more info

Example :

```js

    const params = {
        id              : '0001',
        userIdType      : 'facebook',
        revenue         : 0.0,
        currencyCode    : 'USD',
        advertiserRefId : '00001',
        location        : { latitude : 120.999, longitude : 90.000,  description : '' },
        eventItems      : [{itemName : 'book', unitPrice : 1.00, quantity : 1, revenue : 0.0, attribute1 : '',attribute2 : '',attribute3 : '',attribute4 : '',attribute5 : '' }]    
    };

    TuneSDK.checkoutInitiated( params );

```

* **id (required):** String - id of the customer
* **userIdType (required):** String - the user id type, must be one of these facebook, twitter, google or user if you are using a non-third party id.
* **revenue :** Float - the revenue
* **currencyCode:** String - the currency code of the transaction exp. 'USD'
* **advertiserRefId:** String - advertiser reference Id
* **location:** Object
  * **longitude:** Float - latitude
  * **longitude:** Float - longitude
  * **description** String - description or Street address of the location
* **eventItems:** Array of Objects
  * **event:** Object - example object
    * **itemName:** String - item name
    * **unitPrice:** - Float - unit price of item added to cart
    * **quantity:** - Integer - the quantity number of the item added to cart
    * **revenue:** - Float - the revenue
    * **attribute1:** - String
    * **attribute2:** - String
    * **attribute3:** - String
    * **attribute4:** - String
    * **attribute5:** - String



### Purchase


See the [TUNE SDK docs](https://developers.mobileapptracking.com/mobile-sdks-app-events-ecom-purchase/) for more info

Example :

```js

    const params = {
        id              : '0001',
        userIdType      : 'facebook',
        revenue         : 0.0,
        currencyCode    : 'USD',
        advertiserRefId : '00001',
        location        : { latitude : 120.999, longitude : 90.000,  description : '' },
        eventItems      : [{itemName : 'book', unitPrice : 1.00, quantity : 1, revenue : 0.0, attribute1 : '',attribute2 : '',attribute3 : '',attribute4 : '',attribute5 : '' }]    
    };

    TuneSDK.purchase( params );

```

* **id (required):** String - id of the customer
* **userIdType (required):** String - the user id type, must be one of these facebook, twitter, google or user if you are using a non-third party id.
* **revenue :** Float - the revenue
* **currencyCode:** String - the currency code of the transaction exp. 'USD'
* **advertiserRefId:** String - advertiser reference Id
* **location:** Object
  * **longitude:** Float - latitude
  * **longitude:** Float - longitude
  * **description** String - description or Street address of the location
* **eventItems:** Array of Objects
  * **event:** Object - example object
    * **itemName:** String - item name
    * **unitPrice:** - Float - unit price of item added to cart
    * **quantity:** - Integer - the quantity number of the item added to cart
    * **revenue:** - Float - the revenue
    * **attribute1:** - String
    * **attribute2:** - String
    * **attribute3:** - String
    * **attribute4:** - String
    * **attribute5:** - String



### Reservation


See the [TUNE SDK docs](https://developers.mobileapptracking.com/mobile-sdks-app-events-ecom-reservation/) for more info

Example :

```js

    const params = {
      id              : '0001',
      userIdType      : 'facebook',
      gender          : 'FEMALE',
      age             : 50,
      revenue         : 0.0,
      quantity        : 1,
      currencyCode    : 'USD',
      advertiserRefId : '00001',
      date1           : {day : 1, month : 1, year : 2016},
      date2           : {day : 1, month : 1, year : 2016},
      location        : { latitude : 120.999, longitude : 90.000,  description : '' },
      eventItems      : [{itemName : 'book', unitPrice : 1.00, quantity : 1, revenue : 0.0, attribute1 : '',attribute2 : '',attribute3 : '',attribute4 : '',attribute5 : '' }]
  };

    TuneSDK.reservation( params );

```

* **id (required):** String - id of the customer
* **userIdType (required):** String - the user id type, must be one of these facebook, twitter, google or user if you are using a non-third party id.
* **gender (required):** String - MALE or FEMALE all upper case.
* **age:** Int - user age
* **revenue :** Float - the revenue
* **quantity:** Int
* **currencyCode:** String - the currency code of the transaction exp. 'USD'
* **advertiserRefId:** String - advertiser reference Id
* **location:** Object
  * **longitude:** Float - latitude
  * **longitude:** Float - longitude
  * **description** String - description or Street address of the location
* **date1:** Object
  * **day:**   Int
  * **month:** Int  
  * **year**   Int
* **date2:** Object
  * **day:**   Int
  * **month:** Int  
  * **year**   Int   
* **eventItems:** Array of Objects
  * **event:** Object - example object
    * **itemName:** String - item name
    * **unitPrice:** - Float - unit price of item added to cart
    * **quantity:** - Integer - the quantity number of the item added to cart
    * **revenue:** - Float - the revenue
    * **attribute1:** - String
    * **attribute2:** - String
    * **attribute3:** - String
    * **attribute4:** - String
    * **attribute5:** - String      



## Content



### Content View


See the [TUNE SDK docs](https://developers.mobileapptracking.com/mobile-sdks-app-events-content-view/) for more info

Example :

```js

    const params = {
        id           : '0001',
        userIdType   : 'facebook',
        currencyCode : 'USD',
        location     : { latitude : 120.999, longitude : 90.000,  description : '' },
        eventItems   : [{itemName : 'book', unitPrice : 1.00, quantity : 1, revenue : 0.0, attribute1 : '',attribute2 : '',attribute3 : '',attribute4 : '',attribute5 : '' }]    
    };

    TuneSDK.contentView( params );

```

* **id (required):** String - id of the customer
* **userIdType (required):** String - the user id type, must be one of these facebook, twitter, google or user if you are using a non-third party id.
* **currencyCode:** String - the currency code of the transaction exp. 'USD'
* **location:** Object
  * **longitude:** Float - latitude
  * **longitude:** Float - longitude
  * **description** String - description or Street address of the location
* **eventItems:** Array of Objects
  * **event:** Object - example object
    * **itemName:** String - item name
    * **unitPrice:** - Float - unit price of item added to cart
    * **quantity:** - Integer - the quantity number of the item added to cart
    * **revenue:** - Float - the revenue
    * **attribute1:** - String
    * **attribute2:** - String
    * **attribute3:** - String
    * **attribute4:** - String
    * **attribute5:** - String



### Search


See the [TUNE SDK docs](https://developers.mobileapptracking.com/mobile-sdks-app-events-content-search/) for more info

Example :

```js

    const params = {
        id           : '0001',
        userIdType   : 'facebook',
        quantity     : 1,
        currencyCode : 'USD',
        searchString : 'Searching string is this',
        location     : { latitude : 120.999, longitude : 90.000,  description : '' },
        data1        : {day : 1, month : 1, year : 2016},
        date2        : {day : 1, month : 1, year : 2016},
        eventItems   : [{itemName : 'book', unitPrice : 1.00, quantity : 1, revenue : 0.0, attribute1 : '',attribute2 : '',attribute3 : '',attribute4 : '',attribute5 : '' }]    
    };

    TuneSDK.search( params );

```

* **id (required):** String - id of the customer
* **userIdType (required):** String - the user id type, must be one of these facebook, twitter, google or user if you are using a non-third party id.
* **quantity:** Int
* **currencyCode:** String - the currency code of the transaction exp. 'USD'
* **searchString:** String - search string
* **location:** Object
  * **longitude:** Float - latitude
  * **longitude:** Float - longitude
  * **description** String - description or Street address of the location
* **date1:** Object
  * **day:**   Int
  * **month:** Int  
  * **year**   Int
* **date2:** Object
  * **day:**   Int
  * **month:** Int  
  * **year**   Int   
* **eventItems:** Array of Objects
  * **event:** Object - example object
    * **itemName:** String - item name
    * **unitPrice:** - Float - unit price of item added to cart
    * **quantity:** - Integer - the quantity number of the item added to cart
    * **revenue:** - Float - the revenue
    * **attribute1:** - String
    * **attribute2:** - String
    * **attribute3:** - String
    * **attribute4:** - String
    * **attribute5:** - String



## Gaming



### Invite


See the [TUNE SDK docs](https://developers.mobileapptracking.com/mobile-sdks-app-events-social-invite/) for more info

Example :

```js

    const params = {
        id         : '0001',
        userIdType : 'facebook',
        contentId  : 'Something the user achieved or id reference to that achievement'
    };

    TuneSDK.achievementUnlocked( params );

```

* **id (required):** String - id of the customer
* **userIdType (required):** String - the user id type, must be one of these facebook, twitter, google or user if you are using a non-third party id.
* **contentId (required):** String - Something the user achieved or id reference to that achievement



### Level Achieved


See the [TUNE SDK docs](https://developers.mobileapptracking.com/mobile-sdks-app-events-social-invite/) for more info

Example :

```js

    const params = {
        id         : '0001',
        userIdType : 'facebook',
        level      : 45
    };

    TuneSDK.levelAchieved( params );

```

* **id (required):** String - id of the customer
* **userIdType (required):** String - the user id type, must be one of these facebook, twitter, google or user if you are using a non-third party id.
* **level (required):** Int - level in your game



### Spent Credits


See the [TUNE SDK docs](https://developers.mobileapptracking.com/mobile-sdks-app-events-social-invite/) for more info

Example :

```js

    const params = {
        id         : '0001',
        userIdType : 'facebook',
        credits    : 1009009
    };

    TuneSDK.spentCredits( params );

```

* **id (required):** String - id of the customer
* **userIdType (required):** String - the user id type, must be one of these facebook, twitter, google or user if you are using a non-third party id.
* **credits (required):** Int - credits related to your game



### Tutorial Complete


See the [TUNE SDK docs](https://developers.mobileapptracking.com/mobile-sdks-app-events-social-invite/) for more info

Example :

```js

    const params = {
        id         : '0001',
        userIdType : 'facebook'
    };

    TuneSDK.tutorialComplete( params );

```

* **id (required):** String - id of the customer
* **userIdType (required):** String - the user id type, must be one of these facebook, twitter, google or user if you are using a non-third party id.



## Sharing  



### Invite


See the [TUNE SDK docs](https://developers.mobileapptracking.com/mobile-sdks-app-events-social-invite/) for more info

Example :

```js

    const params = {
        id         : '0001',
        userIdType : 'facebook'
    };

    TuneSDK.invite( params );

```

* **id (required):** String - id of the customer
* **userIdType (required):** String - the user id type, must be one of these facebook, twitter, google or user if you are using a non-third party id.



### Rated


See the [TUNE SDK docs](https://developers.mobileapptracking.com/mobile-sdks-app-events-social-rated/) for more info

Example :

```js

    const params = {
        id         : '0001',
        userIdType : 'facebook',
        rating     : 5.0,
        contentId  : '000001'
    };

    TuneSDK.rated( params );

```

* **id (required):** String - id of the customer
* **userIdType (required):** String - the user id type, must be one of these facebook, twitter, google or user if you are using a non-third party id.
* **rating :** Float - the revenue
* **contentId :** String - any descriptive information about the item being rated



### Share


See the [TUNE SDK docs](https://developers.mobileapptracking.com/mobile-sdks-app-events-social-share/) for more info

Example :

```js

    const params = {
        id         : '0001',
        userIdType : 'facebook'
    };

    TuneSDK.share( params );

```

* **id (required):** String - id of the customer
* **userIdType (required):** String - the user id type, must be one of these facebook, twitter, google or user if you are using a non-third party id.

registerCustomProfileString



## Custom User Profile Variables


See the [TUNE SDK docs](https://developers.mobileapptracking.com/mobile-sdks-app-events-social-share/) for more info


### Register custom profile String

Example :

```js

    const params = {
        name  : 'CustomValueName',
        value : 'Bob'
    };

    TuneSDK.registerCustomProfileString( params );

```

* **name  (required) :** String - name of the custom value
* **value (optional) :** String - the user id type, must be one of these facebook, twitter, google or user if you are using a non-third party id.


### Register custom profile date time

Example :

```js

    const params = {
        name  : 'CustomValueName',
        value : { day : 1, month : 1, year : 2016 }
    };

    TuneSDK.registerCustomProfileDateTime( params );

```

* **name  (required) :** String - name of the custom value
* **value (optional) :** Object - and object containing a day, month, year integer


### Register custom profile number

Example :

```js

    const params = {
        name  : 'CustomValueName',
        value : 1
    };

    TuneSDK.registerCustomProfileNumber( params );

```

* **name  (required) :** String - name of the custom value
* **value (optional) :** Int    - the user id type, must be one of these facebook, twitter, google or user if you are using a non-third party id.


### Register custom profile location

Example :

```js

    const params = {
        name  : 'CustomValueName',
        value : { latitude : 120.999, longitude : 90.000,  description : '' }
    };

    TuneSDK.registerCustomProfileGeolocation( params );

```

* **name  (required) :** String - name of the custom value
* **value (optional) :** Object - containing longitude and latitude values



### Set Custom Profile String Value

Example :

```js

    const params = {
        name  : 'CustomValueName',
        value : 'Bob'
    };

    TuneSDK.setCustomProfileStringValue( params );

```

* name  (required) :** String - name of the custom value
* value (optional) :** String - the user id type, must be one of these facebook, twitter, google or user if you are using a non-third party id.



### Set Custom Profile Date Value

Example :

```js

    const params = {
        name  : 'CustomValueName',
        value : { day : 1, month : 1, year : 2016 }
    };

    TuneSDK.setCustomProfileDate( params );

```

* **name  (required) :** String - name of the custom value
* **value (required) :** Object - and object containing a day, month, year integer


### Set Custom Profile Number Value

Example :

```js

    const params = {
        name  : 'CustomValueName',
        value : 1
    };

    TuneSDK.setCustomProfileNumber( params );

```

* **name  (required) :** String - name of the custom value
* **value (required) :** Int    - the user id type, must be one of these facebook, twitter, google or user if you are using a non-third party id.


### Set Custom Profile Geolocation Value

Example :

```js

    const params = {
        name  : 'CustomValueName',
        value : { latitude : 120.999, longitude : 90.000,  description : '' }
    };

    TuneSDK.setCustomProfileGeolocation( params );

```

* **name  (required) :** String - name of the custom value
* **value (required) :** Object - containing longitude and latitude values


### Clear Custom Profile Variable

Example :

```js    

    TuneSDK.clearCustomProfileVariable( 'NameOfCustomValue' );

```

* **name  (required) :** String - name of the custom value


### Clear All Custom Profile Variables

Example :

```js    

    TuneSDK.clearAllCustomProfileVariables();

```



# In App Marketing Features

  [documentation](https://developers.mobileapptracking.com/enabling-in-app-marketing/)


## Enabling Push

[documentation](https://developers.mobileapptracking.com/enabling-push/)


### iOS
  You will have to follow the instructions here and add the neccessary certs to enable push.


### Android
  For android you will also need to follow the link above for instructions to on how to get your Google Sender Id,
  from Google API developer console. Once you create your app and get the Id, make sure you have these items.

* ** You have your apps configuration find in your projects com.main folder. You can get it from here [Google Configuration](https://developers.google.com/mobile/add?platform=android&cntapi=gcm&cnturl=https:%2F%2Fdevelopers.google.com%2Fcloud-messaging%2Fandroid%2Fclient&cntlbl=Continue%20Adding%20GCM%20Support&%3Fconfigured%3Dtrue)
* ** You add the Google Sender Id to the initializer class that you created earlier ( TuneSDK : in the examples )

```java

  public class TuneSDK extends TuneApplication {

      private static Tune tuneInstance;
      private static final String tuneAdvertiserId = "your_advertisment_id";
      private static final String tuneConversionKey = "your_conversion_id";
      // Google Sender Id if you integrated IAM for push notifications
      private static final String tuneSenderId = "google_sender_id";

        @Override
        public void onCreate() {
            super.onCreate();
            tuneInstance = Tune.init( this, tuneAdvertiserId, tuneConversionKey);

            // Add Google Sender Id
            tuneInstance.setPushNotificationSenderId(tuneSenderId);
        }
    }

```  


## Power Hooks Registration

[documentation](https://developers.mobileapptracking.com/power-hooks-registration/)


### iOS

  For iOS, you just have to add a array to the Tune dictionary you created in the Info.plist. Name the array **powerHooks**. For each hook that you want to
  register, create an dictionary with the fields hookId, hookValue, and hookDefault with your string values for each like this example.


  ![alt tag](https://shashinno.s3.amazonaws.com/tune/dictionary.shot.tune.png)


### Android

  The process for android is a little different. Add your power hooks to the TuneApplication class that you created earlier, right
  after your Tune.init call. See the example below:


  ```java

  	package com.name.of.your.package;

  	import com.tune.Tune;
  	import com.tune.ma.application.TuneApplication;

  	public class TuneSDK extends TuneApplication {

  	    private static Tune tuneInstance;
  	    private static final String tuneAdvertiserId = "your_advertisment_id";
  	    private static final String tuneConversionKey = "your_conversion_id";

  	    @Override
  	    public void onCreate() {
  	        super.onCreate();
  	        tuneInstance = Tune.init( this, tuneAdvertiserId, tuneConversionKey);

  	        // Register your Power Hook
  	        tuneInstance.registerPowerHook("hook_id", "hook_user_friendly_name_value", "hook_default_value");
  	    }

  	}

  ```


Once register, you can retrieve hook values via Javascript by adding the hook id to an array and passing it to the getPowerHookValues async method in your .


  `getPowerHookValues` - Returns a Map to all the values you requested.


  * **name  (required) :** Array - all the hook id's of the values you want to get


  ```javascript

    componentWillMount() {
      this.getYourHookValue(['hook_id']);
    }

    @autobind
    async getYourHookValue(values = []) {
      const hookValues = await TuneSDK.getPowerHookValues(values);

      this.setState({ hookValue : hookValues.feedback_url });

    }

  ```

## Deep Actions

[documentation](https://developers.mobileapptracking.com/deep-actions/)

  TBD
