# Map, GPS, and Location on Android Platform
*Eileen Feng, Dec 2018*


###   `Overview`

Map and location services are very important for mobile apps these days. Applications such as Doordash (delivery services), Waze (navigation and direction services), and social apps that allow user-created content with location information, all involves maps and location services. The sections below introduce how to build these features into your app with [android.location](https://developer.android.com/reference/android/location/package-summary) framework APIs, [Google Play services location APIs](https://developer.android.com/training/location/), and [Google Maps Android API](https://developers.google.com/maps/documentation/android-sdk/intro). 



###   `Maps - Google Maps Android API`

The Google Maps Android API provides a nice abstraction for developers who want to add maps into their applications. It is designed for people who have experience with Android development as well as object-oriented programming. The Maps SDK is based on Google Maps data, and it automatically handles lots of background activities for developers, including connecting to Google Maps servers, downloading datas, and responsing to map gestures. This API also allows users to customize their own maps, such as set up markers or indoor maps. The following content provides an introduction on how to add map activities into your apps, basic customization of your maps, and testing your map-based applications on android emulators. 


####   `How to Add Map into Your Apps`

If you do not have Android Studio on your laptops yet, you can [download](https://developer.android.com/studio/) from the Android developers' website. 

Once you have Android Studio downloaded, choose *Start a new Android Studio project* upon launching. Following the instructions to continue creating the project (fill in application name, projecct location etc.), and for the *Add an Activity to Mobile* step, choose **Google Maps Activity**. 

An API key is required for accessing the Google Maps servers. After creating a new project with Google Maps Activity, Android Studio will open up two files automatically: `google_maps_api.xml` and `MapsActivity.java` (or whatever names you gave for your map activity). Navigate to `google_maps_api.xml`, copy the link provided in this file, and paste it in your browser. Follow the instructions in the corresponding website and obtain an API key for your app. Copy your API key into the `google_maps_key` String object in the `google_maps_api.xml` file. Make sure to keep your API key confidential. The screenshots below elaborates this process. 

- *Copy the link in `google_maps_api.xml` file and paste it in your browser*
 
<p align="center">
 <img src="/image/google_maps_api.png" width="600" height="130" >
</p>

- *Paste your API key to the `google_maps_api.xml` file*

<p align="center">
 <img src="/image/API_key.png" width="700" height="350" >
</p>


Developers are also required to set up the **Google Play services SDK** for their projects in order to use the Maps SDK. To do this, first make sure your top level `build.gradle` contains a reference to `google()` or the corresponding maven dependency: `maven { url "https://maven.google.com" }`. Specifically, you want to check whether your `buildscript` contains the line of code `google()` under `repositories`. Then open the `build.gradle` file for your app, and add dependencies for using the `play-services`. [This website](https://developers.google.com/android/guides/setup) provides a lists of APIs and the corresponding `build.gradle` description. For using the Maps SDK, add a new build rule `implementation com.google.android.gms:play-services-maps:16.0.0` to your `build.gradle`, and sync your project. Now you should be all set up for building your map-based project. 



#### `GoogleMap object and MapFragment class`
