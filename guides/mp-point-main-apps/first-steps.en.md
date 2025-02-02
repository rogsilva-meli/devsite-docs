# First steps

Make sure you have the Demo App installed on the SmartPOS device from the [Development Kit](https://drive.google.com/drive/folders/1Mglpa2c3FmYs4L9iskczagBMPGjHCMbY?usp=share_link). This app can serve as a reference when integrating.

To use the [SDK](/developers/en/docs/sdks-library/landing), follow these steps:

1. Add the Development Kit library to the **app/libs** directory.
2. IInclude the dependency for the library in the **.gradle** file of the module in which the SDK will be used. Like this:

```gradle
dependencies {
     ....
     implementation files("libs/nativesdk-0.1.0.aar")
     ...
}
```

3. In the **AndroidManifest.xml** file, define the main activity that will be set as the application launcher. Add the following `intent-filter`:

```xml
<intent-filter>                
                <action android:name="android.intent.action.MAIN" />               
                <category android:name="android.intent.category.DEFAULT" />               
                <category android:name="android.intent.category.HOME" />
</intent-filter>
```

4. Create the class inherited from the Android [Application](https://developer.android.com/reference/android/app/Application) class and enter the SDK configuration: 

[[[
```kotlin
class MainApplication : Application() {
   override fun onCreate() {
       super.onCreate()
       val config = MPConfigBuilder(this, "123456789")
           .withBluetoothConfig()
           .withBluetoothUIConfig()
           .build()
       MPManager.initialize(this, config)
   }
}
```
```javascript
public class MainApplication extends Application {

 @Override
 public void onCreate() {
   super.onCreate();
   MPConfig config = new MPConfigBuilder(this, "123456789")
     .withBluetoothConfig()
     .withBluetoothUIConfig()
     .build();
   MPManager.INSTANCE.initialize(this, config);
 }
}
```
]]]

5. Start using the SDK to process payments:

[[[
```kotlin
val paymentFlow = MPManager.paymentFlow

val uriSuccess = paymentFlow.buildCallbackUri(
   "mercadopago://launcher_native_app",
   "success",
   hashMapOf("attr" to "123"),
   "demo_app"
)

val uriError = paymentFlow.buildCallbackUri(
   "mercadopago://launcher_native_app",
   "error",
   hashMapOf("attr" to "456"),
   "demo_app"
)

paymentFlow.launchPaymentFlowActivity(
   amount,
   description,
   uriSuccess,
   uriError,
   context,
   lastPaymentMethodSelected
) { response ->
   response.doIfError { error ->
       error.message?.let { errorMessage -> setLayoutError(errorMessage) }
   }
}
```
```javascript
PaymentFlow paymentFlow = MPManager.INSTANCE.getPaymentFlow();

HashMap<String, String> metadata = new HashMap<>();

Uri uriSuccess = paymentFlow.buildCallbackUri(
 "mercadopago://launcher_native_app",
 "success",
 metadata,
 "demo_app"
);

Uri uriError = paymentFlow.buildCallbackUri(
 "mercadopago://launcher_native_app",
 "error",
 metadata,
 "demo_app"
);

Function1<MPResponse, Unit> callback = (MPResponse response) -> {
 return;
};

paymentFlow.launchPaymentFlowActivity(
 "1",
 "My Payment Description",
 uriSuccess,
 uriError,
 getApplicationContext(),
 "credit",
 callback
);
```
]]]