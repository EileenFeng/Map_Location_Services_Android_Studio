# Map, GPS, and Location on Android Platform
*Eileen Feng, Dec 2018*

###   `Overview`

Map and location services are very important for mobile apps these days. Applications such as Doordash (delivery services), Waze (navigation and direction services), and social apps that allow user-created content with location information, all involves maps and location services. The sections below introduce how to build these features into your app with [android.location](https://developer.android.com/reference/android/location/package-summary) framework APIs, [Google Play services location APIs](https://developer.android.com/training/location/), and the [Google Maps Android API](https://developers.google.com/maps/documentation/android-sdk/intro). We will be using Android Studio IDE. If you do not have Android Studio on your laptops yet, you can [download](https://developer.android.com/studio/) from the Android developers' website. 
<br> <br/>

###   `Maps - Google Maps Android API`

The Google Maps Android API provides a nice abstraction for developers who want to add maps into their applications. It is designed for people who have experience with Android development as well as object-oriented programming. The Maps SDK is based on Google Maps data, and it automatically handles lots of background activities for developers, including connecting to Google Maps servers, downloading datas, and responsing to map gestures. This API also allows users to customize their own maps. The following content provides an introduction on how to add map activities into your app, basic customization of your map, and testing your map-based application on android emulator. 

- #### `Set Up Emulators`

It is very important that developers can test their applications on emulators when they do not have an android devices on hand. Before we delve into the code, we will set up our emulators for running and testing our map-based applications. 

First, check your `Android Emulator` tools is up-to-date. Click on `SDK Manager` under `Tools` in your menu, select the `SDK Tools` tab, check `Android Emulator`, and apply your changes to update packages. 

<p align="center">
 <img src="/image/emulator_update.png" width="600" height="400" >
</p>

Then you want to [create a new emulator](https://developer.android.com/studio/run/managing-avds) for running your apps. Make sure to choose an Android version with Google APIs supports. The information of the emulator I am using for testing my map-based app are listed below: 
```
Type: Nexus S 
API 28
Android 9.0(Google APIs)
```

- ####   `Set up your project`

Once you have Android Studio downloaded, choose *Start a new Android Studio project* upon launching. Following the instructions for setting up you project (fill in application name, project location etc.). For the *Add an Activity to Mobile* step, choose **Google Maps Activity** and continue. Android Studio will automatically add a map in your app after you created a new project with map activity. 

An API key is required for accessing the Google Maps servers. After creating a new project with Google Maps Activity, Android Studio will open up two files automatically: `google_maps_api.xml` and `MapsActivity.java` (your map activity class file). Navigate to `google_maps_api.xml`, copy the link provided in this file, and paste it in your browser. Follow the instructions in the corresponding website and obtain an API key for your app. Copy your API key into the `google_maps_key` String object in the `google_maps_api.xml` file. Make sure to keep your API key confidential. The screenshots below elaborates these steps. 

- *Copy the link in `google_maps_api.xml` file and paste it in your browser*
 
<p align="center">
 <img src="/image/google_maps_api.png" width="600" height="130" >
</p>

- *Paste your API key to the `google_maps_api.xml` file*

<p align="center">
 <img src="/image/API_key.png" width="700" height="350" >
</p>


Developers are also required to set up the **Google Play services SDK** for their projects in order to use the Maps SDK. To do this, first make sure your top level `build.gradle` contains a reference to `google()` or the corresponding maven dependency: `maven { url "https://maven.google.com" }`. Specifically, you want to check whether your `buildscript` contains the line of code `google()` under `repositories`. Then open the `build.gradle` file for your app, and add dependencies for using `play-services`. [This website](https://developers.google.com/android/guides/setup) provides a list of APIs and the corresponding `build.gradle` description. For using the Maps SDK, add a new build rule `implementation com.google.android.gms:play-services-maps:16.0.0` to `build.gradle`, and sync your project. 

Now you should be all set up for building your map-based project. 


- ####   `GoogleMap object and MapFragment class`
 
 [`MapFragment`](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment) and [`GoogleMap`](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap) are two of the most important classes for interacting with maps on android apps. We will look into the basic methods and operations for both classes below. 
 
**MapFragment** 

An instance of map object(`GoogleMap`) cannot be obtained directly, but must be acquired by calling `getMapAsync(OnMapReadyCallback)` on either [`MapFragment`](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment) or [`MapView`](https://developers.google.com/android/reference/com/google/android/gms/maps/MapView) instances. In addition, the `MapsActivity` class must implements the [`OnMapReadyCallback`](https://developers.google.com/android/reference/com/google/android/gms/maps/OnMapReadyCallback) interface. This blog will elaborate on the `MapFragment` class as it is the simplest and most common practice for adding maps into applications. 

We can obtain a new instance of `MapFrament` by calling `MapFragment.newInstance()` and add this fragment into our activity with [`FragmentTransaction`](https://developer.android.com/reference/android/app/FragmentTransaction). However, there is a simpler way for doing this. As a fragment, `MapFrament` element can be easily added through xml files. Navigate to your `activity_maps.xml` under `layout`, and you will notice that Android Studio has automatically included a `MapFragment` for you: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:map="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/map"
    android:name="com.google.android.gms.maps.SupportMapFragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MapsActivity" />
```

Then in `MapsActivity.java`, we will find this fragment and set callback:  

```java
@Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_maps);
        /*  // adding MapFragment through code instead of through xml 
            MapFragment mapFragment = MapFragment.newInstance();
            FragmentTransaction fragmentTransaction =
                    getFragmentManager().beginTransaction();
            fragmentTransaction.add(android.R.id.content, mapFragment);
            fragmentTransaction.commit();
         */
        SupportMapFragment mapFragment = (SupportMapFragment) getSupportFragmentManager()
                .findFragmentById(R.id.map);
        mapFragment.getMapAsync(this);
    }

```
 
**GoogleMap** 

As mentioned in the previous section, the `MapsActivity` class needs to implement the [`OnMapReadyCallback`](https://developers.google.com/android/reference/com/google/android/gms/maps/OnMapReadyCallback) interface and overrides the `abstract void	onMapReady(GoogleMap googleMap)` method. This method provides a way of tackling your `GoogleMap` object, and will be called once your map is ready. If you want to customize your `GoogleMap` instance when the app is launched, you can specify your customizations in this method. For instance, we can set up markers and map themes as below: 

```java
@Override
    public void onMapReady(GoogleMap googleMap) {
        googleMap.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
        LatLng zero = new LatLng(0, 0);
        googleMap.addMarker(new MarkerOptions().position(zero).title("Zero"));
        googleMap.moveCamera(CameraUpdateFactory.newLatLng(zero));
    }
```

When we launch the app, we will see a map with satellite's view and a marker at longtitude 0 and latitude 0 on earth, as shown below: 

<p align="center">
 <img src="/image/onMapReady.png" width="250" height="450" >
</p>

The code example above mentioned several methods and objects that are frequently used in setting up map attributes, and we will look into them in more details. 

- `MapType`

The Maps SDK provides various map types, each of them stands for different design and representation of map. In the code example provided above we are using `MAP_TYPE_SATELLITE`. You can always set your map type by calling `setMapType(<map_type>)` on your `GoogleMap` instance. 

- `Marker`

Markers are designed for identifying locations on maps. The [`Marker`](https://developers.google.com/maps/documentation/android-sdk/marker) class receive `click` events by default, therefore can be used for user interactions in maps, for instance providing information when user click on a marker for a particular location. Developers can customize markers in various ways, for instance the color, opacity, and position of markers, or even the click event. To customize the 'on-click' event for markers, your `MapsActivity` has to implements the `OnMarkerClickListener` interface. Below is an example of customized marker, which is positioned at Tokyo. When user click on the marker, a short, text introduction of Tokyo will be presented on the screen.

```java
public class MapsActivity extends FragmentActivity implements OnMapReadyCallback,
        GoogleMap.OnMarkerClickListener {
        
    @Override
    public void onMapReady(GoogleMap googleMap) {
        googleMap.setMapType(GoogleMap.MAP_TYPE_HYBRID);
        LatLng tokyo = new LatLng(35.6895, 139.6917);
        Marker mShrine = googleMap.addMarker(new MarkerOptions()
                                                .position(tokyo)
                                                .snippet("Hello Tykyo")
                                                .icon(BitmapDescriptorFactory
                                                             .defaultMarker(BitmapDescriptorFactory.HUE_MAGENTA))
                                                .title("Tokyo"));

        mShrine.setAlpha(0.7f);
    }

    @Override
    public boolean onMarkerClick(Marker marker) {
        Toast.makeText(this,
                "Tokyo, Japan’s busy capital, mixes the ultramodern and the traditional, from neon-lit skyscrapers to historic temples.",
                Toast.LENGTH_LONG*10).show();
        return true;
    }
}

```

- `CameraUpdateFactory`

To get different views on the map, the Google Map API offered the [`CameraUpdateFactory`](https://developers.google.com/maps/documentation/android-sdk/views#changing_zoom_level_and_setting_minimummaximum_zoom) class. The android developer's documentation has linked above has covered considerable details on how camera positions are related to the views created on map. Here, we will look into an example of how to combine `CameraUpdateFactory` with `Marker` to customize our map, with some simple animations. Below is the example code for customizing a marker at Tokyo. When the application launches, the map will be directed to where the marker `tokyo` is, and gradually zooms into the area near this marker. 

```java
public class MapsActivity extends FragmentActivity implements OnMapReadyCallback,
        GoogleMap.OnMarkerClickListener {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_maps);
        /*  // adding MapFragment through code
            MapFragment mapFragment = MapFragment.newInstance();
            FragmentTransaction fragmentTransaction =
                    getFragmentManager().beginTransaction();
            fragmentTransaction.add(android.R.id.content, mapFragment);
            fragmentTransaction.commit();
         */
        SupportMapFragment mapFragment = (SupportMapFragment) getSupportFragmentManager()
                .findFragmentById(R.id.map);
        mapFragment.getMapAsync(this);
    }


    @Override
    public void onMapReady(GoogleMap googleMap) {
        googleMap.setMapType(GoogleMap.MAP_TYPE_HYBRID);
        LatLng tokyo = new LatLng(35.6895, 139.6917);
        Marker mShrine = googleMap.addMarker(new MarkerOptions()
                                                .position(tokyo)
                                                .snippet("Hello Tykyo")
                                                .icon(BitmapDescriptorFactory
                                                        .defaultMarker(BitmapDescriptorFactory.HUE_MAGENTA))
                                                .title("Tokyo"));

        mShrine.setAlpha(0.7f);
        googleMap.moveCamera(CameraUpdateFactory.newLatLngZoom(tokyo, 1.5f));
        googleMap.animateCamera(CameraUpdateFactory.zoomIn());
        googleMap.animateCamera(CameraUpdateFactory.zoomTo(9.5f), 2000, null);
        UiSettings ui = googleMap.getUiSettings();
        ui.setAllGesturesEnabled(true);
        ui.setCompassEnabled(true);
    }

    @Override
    public boolean onMarkerClick(Marker marker) {
        Toast.makeText(this,
                "Tokyo, Japan’s busy capital, mixes the ultramodern and the traditional, from neon-lit skyscrapers to historic temples.",
                Toast.LENGTH_LONG*10).show();
        return true;
    }
}

```

Below is the screenshot after the camera has zoomed in to the Tokyo area. 

<p align="center">
 <img src="/image/tokyo.png" width="250" height="450" >
</p>

The `moveCamera(CameraUpdate <location/marker>)` function will moves the camera to the positions specified by the marker passed in, and the `animateCamera(CameraUpdate <new_position>)` function will animate the process of moving the camera from the current position to the `new_position` that got passed in. There are many other ways of customizing your own views by manipulating with `CameraUpdate` differently, and this [`documentation`](https://developers.google.com/maps/documentation/android-sdk/views) provided more details in how to create different views by manipulating your `CameraUpdate` differently. 


- `Google Map APIs Developers' Doc`

The `Google Map APIs` allow developers to build their in-app maps in many different ways depending on specific context, for instance what information can users to obtain through maps, or how users can interact with the in-app maps. The sections above only introduced the basic yet most commonly used classes and strategies in building maps into applications. For more information, please refer to the [developers' documentation for Google Map APIs](https://developers.google.com/maps/documentation/android-sdk/intro).
<br> <br/>

###  `Location Services on Android`

Android apps can access location services through classes in the `android.location` package. The most significant class in the location API is the [`LocationManager`](https://developer.android.com/reference/android/location/LocationManager) class, which allows developers to access and update user location easily. 

Accessing user's location can be challenging for the following reasons. First of all, there are multiple resources for obtaining user locaiton, which includes GPS, CELL-ID, and Wi-Fi. When more than one resources are available, it is hard yet siginicant to determine which resource is more reliable. Secondly, it is challenging to choose the most accurate location provided, as sometimes the newly updated location might be less accurate than the old ones. Thirdly, accessing and updating locations with GPS or network consumes significant amount of batteries, and proper strategies need to be used to perserve as much battery as possible for users. 

- #### `Preserve Battery`

Depends on the specific applications that require location services, developers use different strategies for preserving battery for users. Generally, it is important to shorten the time duration of listening location updates. One useful and common strategy is to start listening for location update only when user starts activities that requires location information, and stop listening as soon as user stops the corresponding activity. The diagram below illustrates this procedure: 

<p align="center">
 <img src="/image/location_listener.png" width="700" height="200" >
</p>

- #### `User Permission for Location Service`

As other system services, android applications must ask for user's permission for accessing location data. Below are the manifest for asking user permissions: 

```xml
 <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
 <!-- If using Android 5.0 or higher, please also add the code below -->
 <uses-feature android:name="android.hardware.location.gps" />
```

- #### `Obtain and Update User Location`

As with other system services, we cannot create a `LocationManager`, rather, we have to request on by calling [`getSystemService(Context.LOCATION_SERVICE)`](https://developer.android.com/reference/android/content/Context#getSystemService(java.lang.String)). Then we need to create a `LocationListener` and specifies the actions we want to perform when user locations changes. Finally we will set up the `LocationListerner` to start listening by calling [`requestLocationUpdates()`](https://developer.android.com/reference/android/location/LocationManager.html#requestLocationUpdates(long,%20float,%20android.location.Criteria,%20android.app.PendingIntent)). We can customize our providers in `requestLocationUpdates()` as `GPS_PROVIDER`, `NETWORK_PROVIDER`, or both. Below is a code example for obtaining `LocationManager`, set up listeners, and remove listener (that is, stop listening user location changes). 

```java
public class MapsActivity extends FragmentActivity implements ... {

    protected LocationManager locManager;
    protected LocationListener locListener;
    protected Location cur_best_location;
    protected long MUST_UPDATE_TIME = 1000 * 60;
    protected int ACCURACY_RANGE = 100;
    protected long MIN_TIME = 60 * 1000;
    protected float MIN_DISTANCE = 10;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_maps);
        cur_best_location = null;
        // obtain `LocationManager`
        locManager = (LocationManager) this.getSystemService(Context.LOCATION_SERVICE);
        // create a `LocationListener`
        locListener = new LocationListener() {
            @Override
            public void onLocationChanged( {
                if (locManager != null) {
                    if (ActivityCompat.checkSelfPermission(getApplicationContext(), Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED &&
                            ActivityCompat.checkSelfPermission(getApplicationContext(), Manifest.permission.ACCESS_COARSE_LOCATION) != PackageManager.PERMISSION_GRANTED) {
                        Toast.makeText(getApplicationContext(), "Please allow access to location services for running this app,", Toast.LENGTH_LONG*2).show();
                        return;
                    }
                    Location lastLoc = locManager.getLastKnownLocation(LocationManager.NETWORK_PROVIDER);
                    Toast.makeText(getApplicationContext(),
                            "Latitude " + lastLoc.getLatitude() + " Longtitude: " + lastLoc.getLongitude(),
                            Toast.LENGTH_LONG*2).show();
                    updateBestLocation(location);
                }
            }

            @Override
            public void onStatusChanged(String s, int i, Bundle bundle) {}

            @Override
            public void onProviderEnabled(String s) {}

            @Override
            public void onProviderDisabled(String s) {}
        };
  
        // check that if user granted location permission, start listening 
        if (ActivityCompat.checkSelfPermission(getApplicationContext(), Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED &&
                ActivityCompat.checkSelfPermission(getApplicationContext(), Manifest.permission.ACCESS_COARSE_LOCATION) != PackageManager.PERMISSION_GRANTED) {
            Toast.makeText(getApplicationContext(), "Please allow access to location services for running this app,", Toast.LENGTH_LONG*2).show();
            return;
        } else {
            /*
                //If prefer GPS as provider:
                locManager.requestLocationUpdates(LocationManager.GPS_PROVIDER, MIN_TIME, MIN_DISTANCE, locListener);
                // If prefer both GPS and NETWORK provider, call `requestLocationUpdates` twice on both:
                locManager.requestLocationUpdates(LocationManager.NETWORK_PROVIDER, MIN_TIME, MIN_DISTANCE, locListener);
                locManager.requestLocationUpdates(LocationManager.GPS_PROVIDER, MIN_TIME, MIN_DISTANCE, locListener);
             */
            locManager.requestLocationUpdates(LocationManager.NETWORK_PROVIDER, MIN_TIME, MIN_DISTANCE, locListener);
        }
    }
 
    // update `cur_best_location` if necessary
    public void updateBestLocation(Location newLoc) {
        if(cur_best_location == null) {
            newLoc = cur_best_location;
            return;
        }
        if(newLoc.getTime() - cur_best_location.getTime() >= MUST_UPDATE_TIME) {
            cur_best_location = newLoc;
            return;
        }
        if(newLoc.getProvider() != null &&
                cur_best_location.getProvider() != null &&
                (newLoc.getProvider().equals(cur_best_location.getProvider()))) {
            if(newLoc.getAccuracy() - cur_best_location.getAccuracy() < 0) {
                cur_best_location = newLoc;
                return;
            }
        }
        int accuracy_diff = (int)(newLoc.getAccuracy() - cur_best_location.getAccuracy());
        if(accuracy_diff < 0) {
            cur_best_location = newLoc;
        } else if (accuracy_diff < ACCURACY_RANGE) {
            cur_best_location = newLoc;
        }
        return;
    }
    
    
    @Override
    public void onStop() {
        super.onStop();
        // stops listening location changes when activity stops
        if(locManager != null && locListener != null) {
            locManager.removeUpdates(locListener);
        }
    }
}

```

One thing that worth pointing out in the example above is that, after we obtained our `LocationManager` and created the `LocationListener`, it is important to check if user has granted access to location services before we start listening. Specifically, we should check user permissions before we perform any actions that relates to location services. 

As requesting location update consumes significant amount of battery, it is a common practice for developers to store the current best location and request location updates based on certain time or distance intervals. As shown in the example above, the `MIN_TIME` and `MIN_DISTANCE` arguments passed into the `requestLocationUpdates()` function specifies the time and distance between each updates. Whenever there is a location update, as set up in the `onLocationChanged()` function of our `LocationListener`, we will call the `public void updateBestLocation(Location newLoc)` function to update the current best locations if necessary. 

There are many ways to decide whether the newly updated location will be chosed over the current best location stored. In the `void updateBestLocation(Location newLoc)` function, we use three factors to determine whether we update our `cur_best_location` or not: time difference between the current best location and the new location, accuracy difference, and resource provider of the new locations. Specifically, we will update our current best location if it has not been updated for a given amount of time (specified by `MUST_UPDATE_TIME`), or the new location is more accurate. In addition, as the provider of our current best location is considered as reliable, if the new location is from the same provider as our `cur_best_location`, and is not significantly less accurate, then we update our best location as well. 


- #### `Android Framework API and Google Play services location APIs`

The location service API introduced above is from the `android.location` packages. Currently there is a more powerful and efficient API provided for location services on Android app, which is the **Google Play services location APIs**. **Google Play services location APIs** is similar to the android framework API, however provides automatic location tracking and improves battery usage. For the purpose of introducing concepts, ideologies, and common practices in building apps with location services, the android framework API for location services is chosen as it is more logically clear and straightforward. For future referrences, the android developers' guide has provided a detailed and helpful description over the [`Google Play services location APIs`](https://developer.android.com/training/location/). 

