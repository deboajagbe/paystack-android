[![API](https://img.shields.io/badge/API-16%2B-blue.svg?style=plastic)](https://android-arsenal.com/api?level=16)




## Paystack Android

This is a library for easy integration of [Paystack](https://paystack.com) with your Android application. Use this
library in your Android app so we shoulder the burden of PCI compliance by helping you
avoid the need to send card data directly to your server. Instead, this library sends credit
card data directly to our servers.

> We have retired the createToken function. Please upgrade your existing apps and use `chargeCard` instead.

## Requirements
- Android SDKv16 (Android 4.1 "Jelly Bean") - This is the first SDK version that includes
`TLSv1.2` which is required by our servers. Native app support for user devices older than
API 16 will not be available.

## Installation

### Android Studio (using Gradle)
You do not need to clone this repository or download the files. Just add the following lines to your app's `build.gradle`:

```gradle
dependencies {
  compile 'co.paystack.android:paystack:2.1.2'
}
```

### Eclipse
To use this library with Eclipse, you need to:

1. Clone the repository.
2. Import the **Paystack** folder into your [Eclipse](http://help.eclipse.org/juno/topic/org.eclipse.platform.doc.user/tasks/tasks-importproject.htm) project
3. In your project settings, add the **Paystack** project under the Libraries section of the Android category.

## Usage

### 0. Prepare for use

To prepare for use, you must ensure that your app has internet permissions by making sure the `uses-permission` line below is present in the AndroidManifest.xml.

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

### 1. initializeSdk

To use the Paystack Android SDK, you need to first initialize it using the `PaystackSdk` class.

```java
class Application{
    @Override
    public void onCreate() {
        super.onCreate();

        PaystackSdk.initialize(getApplicationContext());
    }
}
```

Make sure to call this method in the `onCreate` method of your Fragment or Activity.

### 2. setPublicKey

Before you can charge a card with the `PaystackSdk` class, you need to set your public key. The library provides two approaches,

#### a. Add the following lines to the `<application></application>` tag of your AndroidManifest.xml

```xml
<meta-data
    android:name="co.paystack.android.PublicKey"
    android:value="your public key obtained from: https://dashboard.paystack.co/#/settings/developer"/>
```

#### b. Set the public key by code

```java
class Bootstrap {
    public static void setPaystackKey(String publicKey) {
        PaystackSdk.setPublicKey(publicKey);
    }
}
```

### 3. chargeCard
Charging with the PaystackSdk is quite straightforward.
```java
class chargingActivity {
   public void performCharge(){
       // Create a charge.
       Charge charge = new Charge();
       // Add card to the charge.
       charge.setCard(new Card.Builder(cardNumber, expiryMonth, expiryYear, cvc).build());
       // Add an email for customer
       charge.setEmail(email);
       // Add amount to charge.
       // setAmount() accepts the kobo value
       // which is: the naira value multiplied by 100.
       charge.setAmount(amount);
       // Remember to use a unique reference from your server each time.
       // If you decide not to set a reference, we will provide a value
       // in that case.
       //        charge.setReference("7073397683");

       // Our SDK is Split Payments Aware.
       // You may also set a subaccount, transaction_charge and bearer.
       // Remember that only when a subaccount is provided will the rest be used.
       // charge.setSubaccount("ACCT_azbwwp4s9jidin0iq")
       //        .setBearer(Charge.Bearer.subaccount)
       //        .setTransactionCharge(18);
       // Charge card
       PaystackSdk.chargeCard(activity, charge, new Paystack.TransactionCallback() {
           @Override
           public void onSuccess(Transaction transaction) {
               // This is called only after transaction is deemed successful.
               // Retrieve the transaction, and send its reference to your server
               // for verification.
           }

           @Override
           public void beforeValidate(Transaction transaction) {
               // This is called only before requesting OTP.
               // Save reference so you may send to server. If
               // error occurs with OTP, you should still verify on server.
           }

           @Override
           public void onError(Throwable error) {
             //handle error here
           }

       });
   }
}
```
The first argument to the PaystackSdk.chargeCard is the calling Activity object. Always
give an Activity that will stay open till the end of the transaction. The currently
open Activity is just fine.

### 4. Verifying the transaction
Send the reference to your server and verify by calling our REST API. An authorization will be returned which
will let you know if its code is reusable. You can learn more about our verify call [here](https://developers.paystack.co/docs/verifying-transactions).

### 5. Charging Returning Customers
See details for charging returning customers [here](https://developers.paystack.co/docs/charging-returning-customers).

### 6. Library aided card validation & utility methods
The library provides validation methods to validate the fields of the card.

#### card.validNumber
This method helps to perform a check if the card number is valid.

#### card.validCVC
Method that checks if the card security code is valid.

#### card.validExpiryDate
Method checks if the expiry date (combination of year and month) is valid.

#### card.isValid
Method to check if the card is valid. Always do this check, before charging the card.

#### card.getType
This method returns an estimate of the string representation of the card type.

## Testing your implementation
You can (and should) test your implementation of the Paystack Android library in your Android app. You need the details of an
actual debit/credit card to do this, so we provide ##_test cards_## for your use instead of using your own debit/credit cards. 
You may find test cards on [this Paystack documentation page](https://developers.paystack.co/docs/test-cards).

## Building the example project

1. Clone the repository.
2. Import the project either using Android Studio or Eclipse
3. Add your public key to your AndroidManifest.xml file
4. Build and run the project on your device or emulator

## Contact

For more enquiries and technical questions regarding the Android PaystackSdk, please send an email to support@paystack.com
