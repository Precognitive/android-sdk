# Cognition Android SDK

The goal of the Mobile SDK Documentation is to give Android developers the ability to implement device intelligence and behavior analytics for use with Precognitive's fraud prevention platform.

## Requirements

- Android 4.4+ (KitKat)
- Android Studio 3+

## Installation

Please follow the instructions below to install the SDK in your application:

1. Download the latest version of the Cognition Android SDK.

[cognition-2.1.2.aar](/lib/cognition-2.1.2.aar) [older versions](/lib/index.html)

2. Add the SDK to your project:
    * Click File > New > New Module.
    * Click Import .JAR/.AAR Package then click Next.
    * Enter the location of cognition-n.n.n.aar then click Finish.
    
3. Make sure the library is listed at the top of your settings.gradle file, as shown here for a library named "cognition-release":

```gradle
include ':app', ':cognition-release'
```

4. Open the app module's build.gradle file and add a new line to the dependencies block as shown in the following snippet:

```gradle
dependencies {
    implementation project(":cognition-release")
    
    // AppCompat is required for checking permissions
    implementation 'androidx.appcompat:appcompat:1.1.0'
}
```

5. Click Sync Project with Gradle Files.

**NOTE**: You may need to invalidate your cache & restart.

**source:** [Create an Android library](https://developer.android.com/studio/projects/android-library)

## Usage & Integration

Cognition utilizes background processing to handle fingerprinting. You must implement methods for different
portions of the Activity LifeCycle. 

**Example**
```java
import android.app.Application;

import cognition.android.Cognition;
import cognition.android.CognitionDataOpt;
import cognition.android.CognitionLifeCycleCallbacks;
import cognition.android.CognitionLogLevel;

public class DemoApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();

        // Build standard configs
        Cognition.Config config = new Cognition.Config();

        // Example: config data
        config.setApiKey(CustomConfig.COGNITION_API_KEY);
        config.setBehaviorUrl(CustomConfig.COGNITION_URL_BEHAVIOR); // "https://edge.ad1x.com"
        config.setDeviceUrl(CustomConfig.COGNITION_URL_DEVICE); // "https://cdn.ad1x.com"
        config.setLogLevel(CognitionLogLevel.VERBOSE);
        config.setInterval(Cognition.Config.INTERVAL_NORMAL);
        config.setSensorDelay(Cognition.Config.SENSOR_DELAY_NORMAL);

        // Example: eventId and the userId
        Cognition.setEventId(getServerSessionId());
        Cognition.setUserId(getActiveUserId());

        // Start Cognition!
        Cognition.start(this, config.initialize());

        // Register Cognition Activity Lifecycle Callbacks.
        registerActivityLifecycleCallbacks(new CognitionLifeCycleCallbacks());
    }
}
```

`registerActivityLifecycleCallbacks(new CognitionLifeCycleCallbacks());` starts and stops the
Cognition listener using Android's LifeCycle events.

If you require more control of Cognition, remove the registerActivityLifecycleCallbacks line
above, and manually call Cognition.resume(context) to start the Cognition service
and Cognition.pause() to stop the Cognition service as desired.

**Example**
```java
import android.app.Application;

import cognition.android.Cognition;
import cognition.android.CognitionDataOpt;
import cognition.android.CognitionLifeCycleCallbacks;
import cognition.android.CognitionLogLevel;

public class DemoApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();

        // Build standard configs
        Cognition.Config config = new Cognition.Config();

        // Example: config data
        config.setApiKey(CustomConfig.COGNITION_API_KEY);
        config.setBehaviorUrl(CustomConfig.COGNITION_URL_BEHAVIOR); // "https://edge.ad1x.com"
        config.setDeviceUrl(CustomConfig.COGNITION_URL_DEVICE); // "https://cdn.ad1x.com"
        config.setLogLevel(CognitionLogLevel.VERBOSE);
        config.setInterval(Cognition.Config.INTERVAL_NORMAL);
        config.setSensorDelay(Cognition.Config.SENSOR_DELAY_NORMAL);

        // Example: eventId and the userId
        Cognition.setEventId(getServerSessionId());
        Cognition.setUserId(getActiveUserId());

        // Start Cognition!
        Cognition.start(this, config.initialize());
    }
}
```

```java
import cognition.android.Cognition;

public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Example: eventId and the userId
        Cognition.setEventId(getServerSessionId());
        Cognition.setUserId(getActiveUserId());
    }

    @Override
    protected void onPause() {
        super.onPause();
        Cognition.pause();
    }

    @Override
    protected void onResume() {
        super.onResume();
        Cognition.resume(this);
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        Cognition.close();
    }
}
```

### Available URLs

The below URLs are available for use by your organization as needed. Please follow the instructions of the Precognitive team in regards
to which URLs you should leverage.

All data storage locations are with-in Google Cloud Platform (GCP) - [View Regions](https://cloud.google.com/about/locations#regions-notes).

| Url                            | Name                       | Environment         | Data Storage Location(s)             |
|--------------------------------|----------------------------|---------------------|--------------------------------------|
| https://cdn.ad1x.com           | Device Global Production   | production          | us-central1, us-east1, us-west1      |
| https://edge.ad1x.com          | Behavior Global Production | production          | us-central1, us-east1, us-west1      |
| https://eu-cdn.ad1x.com        | Device EU Production       | production          | europe-west1, europe-west3           |
| https://eu-edge.ad1x.com       | Behavior EU Production     | production          | europe-west1, europe-west3           |
| https://stg-cdn.ad1x.com       | Device Global Staging      | staging/development | us-central1, us-east1, us-west1      |
| https://stg-edge.ad1x.com      | Behavior Global Staging    | staging/development | us-central1, us-east1, us-west1      |

**EU Organizations**

Organizations residing in the EU or operating in the EU should leverage the EU production URLs. EU customers will still utilize 
the Global Staging URLs for testing. You **MUST NOT** send any sort of PII data to the Global staging environments as they do
not reside with in the EU.

### Required Permissions

This permission must be added to your manifest.

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="com.precognitive.demo">

    <!-- Add in permissions -->
    <uses-permission android:name="android.permission.INTERNET" />

</manifest>
```

### Desired Permissions

The following permissions should be added to your manifest if possible.

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="com.precognitive.demo">

    <!-- Add in permissions -->
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <!-- OR -->
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.BLUETOOTH" />
    <uses-permission android:name="com.google.android.providers.gsf.permission.READ_GSERVICES" />

</manifest>
```

## SDK API Documentation

### Class cognition.android.Cognition

The Cognition Class is the main interface for working with the SDK. This can be used to update certain data points such as a UserID after a user has logged in or to retrieve data such as sessionId or deviceId.

| Method                    | Params                                                                          | Returns               | Description                                                                                                                            |
|---------------------------|---------------------------------------------------------------------------------|-----------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| analyze                   | Context context                                                                 | void                  | Used to automatically run all monitoring and submit all data. This can be submitted prior to processing an order, login etc.           |
| analyze                   | Context context, cognition.android.Cognition.Config config                      | void                  | Used to automatically run all monitoring and submit all data. This can be submitted prior to processing an order, login etc.           |
| analyzeBiometrics         | Context context                                                                 | void                  | Used to automatically sample biometrics data and submit to API. This can be submitted prior a login.                                   |
| analyzeBiometrics         | Context context, cognition.android.Cognition.Config config                      | void                  | Used to automatically sample biometrics data and submit to API. This can be submitted prior a login.                                   |
| addUsernameListener       | TextView view                                                                   | void                  | Pass in a TextView field to listen to the Keystroke Dynamics for the user. This is associated to the username field.                   |
| addPasswordListener       | TextView view                                                                   | void                  | Pass in a TextView field to listen to the Keystroke Dynamics for the user. This is associated to the password field.                   |
| removeUsernameListener    | TextView view                                                                   | void                  | Used to remove the username field listener.                                                                                            |
| removePasswordListener    | TextView view                                                                   | void                  | Used to remove the password field listener.                                                                                            |
| addPageView               | String value, JSONObject metadata                                               | void                  | User intent meant to represent a "page view" this can be used when navigating between views.                                           |
| addSearch                 | String value, JSONObject metadata                                               | void                  | User intent meant to represent a user "search". This should be used when users use a search bar or similar UI component.               |
| addObjectView             | String value, JSONObject metadata                                               | void                  | User intent meant to represent a specific "Object", i.e. a Credit Card, Loan or Product.                                               |
| addIntentGroup            | String value, JSONObject metadata                                               | void                  | User intent meant to represent a specific intent group, i.e. "jeans" or "loans".                                                       |
| setEventId                | String eventId                                                                  | void                  | This method can be used to update the eventId at any given time to meet your logical requirements.                                     |
| setUserId                 | String userId                                                                   | void                  | This method can be used to update the user at any given time, such as after the user has logged in.                                    |
| getSessionId              |                                                                                 | String                | This method returns the sessionID that can be passed along with a request for decisioning                                              |
| resetSession              |                                                                                 | void                  | This method is used to reset a session. **This should be used if a user logs out of the app.**                                         |
| start                     | Context context, cognition.android.Cognition.Config config                      | void                  | Starts the behavior and device monitoring lifecycle.                                                                                   |
| pause                     |                                                                                 | void                  | Pauses the behavior and device monitoring lifecycle.                                                                                   |
| resume                    | Context context                                                                 | void                  | Resumes the behavior and device monitoring lifecycle.                                                                                  |
| close                     |                                                                                 | void                  | Stops & closes the behavior and device monitoring lifecycle.                                                                           |

### Class cognition.android.Cognition.CognitionDataOpt

The CognitionDataOpt enum is utilized to opt-in and opt-out of certain data points. The majority of data points are enabled by default and should not be removed without good reason and guidance from
the Precognitive team. Location by default is disabled and should be added only if your application already requests Location permissions.

| Enum                      | Default State             | Examples                                                         |
|---------------------------|---------------------------|------------------------------------------------------------------|
| HARDWARE_DEVICE           | enabled                   | screenHeight, screenWidth, buildBrand                            |
| HARDWARE_APP_INFO         | enabled                   | appVersionName, appVersionCode, userAgent.                       |
| HARDWARE_MEMORY           | enabled                   | totalRAM, availableInternalMemorySize.                           |
| HARDWARE_NETWORK          | enabled                   | networkClass, networkType.                                       |
| HARDWARE_BATTERY          | enabled                   | batteryPercent, batteryHealth, chargingSource                    |
| SENSOR_ACCELEROMETER      | enabled                   | x, y, z, accuracy                                                |
| SENSOR_PRESSURE           | enabled                   | altitude, pressure                                               |
| SENSOR_MAGNETIC_FIELD     | enabled                   | x, y, z, accuracy                                                |
| SENSOR_ROTATION_VECTOR    | enabled                   | x, y, z, headingAccuracy                                         |
| SENSOR_GYROSCOPE          | enabled                   | x, y, z, accuracy                                                |
| SENSOR_GRAVITY            | enabled                   | x, y, z, accuracy                                                |
| SENSOR_KEYSTROKE_DYNAMICS | enabled                   | keystrokes, timestamp                                            |
| SENSOR_LOCATION           | disabled                  | latitude, longitude, speed, bearing                              |

**NOTE:** The majority of data lookups are wrapped in permission checks. This means if you opt-in but don't provide permissions no data will be collected.

### Class cognition.android.Cognition.Config

The Config class is utilized to build out the configuration for the Cognition SDK.

| Method              | Params                                                              | Returns                                   | Description                                                                                                 |
|---------------------|---------------------------------------------------------------------|-------------------------------------------|-------------------------------------------------------------------------------------------------------------|
| setApiKey           | String apiKey                                                       | cognition.android.Cognition.Config        | Utilized to set you API key identifier.                                                                     |
| setBehaviorUrl      | String url                                                          | cognition.android.Cognition.Config        | Behavior URL which is utilized to send requests to our server or a proxy (not recommended).                 |
| setDeviceUrl        | String url                                                          | cognition.android.Cognition.Config        | Device URL which is utilized to send requests to our server or a proxy (not recommended).                   |
| setInterval         | long interval (use constants below)                                 | cognition.android.Cognition.Config        | Frequency at which our SDK will reach out to our servers to collect data on the device.                     |
| setSensorDelay      | int delay (use constants below)                                     | cognition.android.Cognition.Config        | Frequency at which our SDK will sample the device sensors for data.                                         |
| setLogLevel         | cognition.android.Cognition.CognitionLogLevel logLevel              | cognition.android.Cognition.Config        | Used to set the log level for the SDK, follows standard ERROR, VERBOSE, WARN terminology.                   |
| optIn               | cognition.android.Cognition.CognitionDataOpt optInGroup             | cognition.android.Cognition.Config        | Used to opt-in to a data collection group. Certain data groups are automatically enabled (i.e. Keystrokes). |
| optOut              | cognition.android.Cognition.CognitionDataOpt optOutGroup            | cognition.android.Cognition.Config        | Used to opt-out of a data collection group. Certain data groups are automatically disabled (i.e. Location). |
| initialize**        |                                                                     | cognition.android.Cognition.Config        | Builds the final config object to be passed to the Cognition.start method.                                  |

**NOTE:** If you do NOT call `config.initialize()` prior to calling `Cognition.start()` an uncaught Exception will be thrown. You MUST call `initialize`.

| Field               | Type         | Description                                                                                            |
|---------------------|--------------|--------------------------------------------------------------------------------------------------------|
| SENSOR_DELAY_NORMAL | int          | Standard sensor sampling used to produce a standard subset of data.                                    |
| SENSOR_DELAY_FAST   | int          | Frequent sensor sampling which will collect more data, and can possibly impact performance.            |
| SENSOR_DELAY_SLOW   | int          | Less frequent sensor sampling which will minimize impact but still produce a usable data set.          |
| INTERVAL_NORMAL     | long         | Standard interval used to produce a standard data-set.                                                 |
| INTERVAL_FAST       | long         | Frequent interval used to produce a larger data-set, and can possibly impact performance.              |
| INTERVAL_SLOW       | long         | Less frequent interval which will minimize impact but still produce a usable data set.                 |

## Troubleshooting
If you have any issues installing the SDK, please reach out to [support@precognitive.com](mailto:support@precognitive.com) and we will get back to you regarding your issues/problems with the SDK.
