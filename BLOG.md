# Map, GPS, and Location on Android Platform
*Eileen Feng, Dec 2018*

###   `Overview`

Map and location services are very important for mobile apps these days. Applications such as Doordash (delivery services), Waze (navigation and direction services), and social apps that allow user-created content with location information, all involves maps and location services. The sections below introduce how to build these features into your app with [android.location](https://developer.android.com/reference/android/location/package-summary) framework APIs, [Google Play services location APIs](https://developer.android.com/training/location/), and the [Google Maps Android API](https://developers.google.com/maps/documentation/android-sdk/intro). 
<br> <br/>
###   `Maps - Google Maps Android API`

The Google Maps Android API provides a nice abstraction for developers who want to add maps into their applications. It is designed for people who have experience with Android development as well as object-oriented programming. The Maps SDK is based on Google Maps data, and it automatically handles lots of background activities for developers, including connecting to Google Maps servers, downloading datas, and responsing to map gestures. This API also allows users to customize their own maps. The following content provides an introduction on how to add map activities into your app, basic customization of your map, and testing your map-based application on android emulator. 

- ####   `Set up your project`

If you do not have Android Studio on your laptops yet, you can [download](https://developer.android.com/studio/) from the Android developers' website. 

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
        googleMap.getUiSettings().setAllGesturesEnabled(true);
    }
```

When we launch the app, we will see a map with satellite's view and a marker at longtitude 0 and latitude 0 on earth, as shown below: 

<p align="center">
 <img src="/image/onMapReady.png" width="250" height="450" >
</p>

The code example above mentioned several methods and objects that are frequently used in setting up map attributes, and we will look into them in more details. 

- `MapType`
- `Marker`
- `CameraUpdateFactory`
- `Uisettings`
