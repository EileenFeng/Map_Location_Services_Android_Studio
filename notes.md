# Map, GPS, and Location Services on Android Platform
### by Eileen Feng, Dec 2018


- mastering markdown : https://guides.github.com/features/mastering-markdown/

- Reference base: https://developer.android.com/guide/topics/location/

- Location:
main reference for location strategies : https://developer.android.com/guide/topics/location/strategies
location manager: https://developer.android.com/reference/android/location/LocationManager

1. utilize GPS and Android's Network Location Provider for acquiring user location
(a) GPS accurate, but only works outdoors, consumes battery
- testing : geo, developor options: bottom of the page 




- Maps:
- Maps SDK for Android: google mapsL https://developers.google.com/maps/documentation/android-sdk/start
- needs to add google play services to android studio: https://developers.google.com/android/guides/setup

- For maps project, need to add google maps activity

- when testing maps on emulators, make sure to select an emulator with google APIs

2. tutorials for describing the map object: https://developers.google.com/maps/documentation/android-sdk/map
- can add a map fragment both through xml <fragment> or through code
- compare to the google map app, map on android apps have the following limitations:
  - Map tiles displayed by the API don't contain any personalized content, such as personalized smart icons.
  - Not all icons on the map are clickable. For example, transit station icons can’t be clicked. However, markers that you add to the map are clickable, and the API has a listener callback interface for various marker interactions.

  - Map object features: allows us to configure maps:introduction,  set marker, set map type, street views.
   - Maybe launch google maps part: intent
   - styling
   - marker: set location, draggable