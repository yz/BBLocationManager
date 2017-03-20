# BBLocationManager

A Location Manager for easily implementing location services & geofencing in iOS, written in Objective-C.

## Features
* Get current/continious/frequent location and get current geocode/address.
* Add or remove [Geofence](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/LocationAwarenessPG/RegionMonitoring/RegionMonitoring.html) at current/given location. Get callback via delegate when user enter/exit a geofence, supports foreground/background, even when [app is not running](https://developer.apple.com/reference/corelocation/cllocationmanager#//apple_ref/doc/uid/TP40007125-CH3-SW32).
* Read location permission status and ask for permisssion.
* High performance, easy to use, battery friendly, use via block or delegate. 
* Example App included for demonstrating all the features.

![Current location and GeoCode](https://raw.githubusercontent.com/benzamin/BBLocationManager/master/screens/locationNgeofence.gif  "Getting current location and Geocode")

## Getting Started
[Location services](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/LocationAwarenessPG/CoreLocation/CoreLocation.html) is a powerful feature of iOS, but sometimes its not easy to understand all the API's and learn how to use them. With BBLocationManager, you can start using iOS Location Services API in no time. It provides good code documentation for better understanding of the methods and delegates. If you are making a location aware app or building a geofencing app like the [Alarm](https://developer.apple.com/library/content/documentation/DataManagement/Conceptual/EventKitProgGuide/ConfiguringAlarms/ConfiguringAlarms.html) app in iOS which reminds you to buy milk when you are near home, BBLocationManager can be your choice.


## Installation
BBLocationManager can be installed through Cocoapods or manually. You can check out the example project by [downloading the full source code](https://github.com/benzamin/BBLocationManager/archive/master.zip)

### Installing with CocoaPods

[CocoaPods](http://cocoapods.org) is very popular dependency manager for iOS projects. It automates and simplifies the process of using 3rd-party libraries like BBLocation in your projects. If you don't have cocoapods installed in your mac already, you can install it with the following command:

```bash
$ gem install cocoapods
```

#### Podfile
If you already have a Podfile, add the following line in your podfile:

```ruby
pod 'BBLocationManager'
```

If you already dont have a podfile, To integrate BBLocationManager into your Xcode project using CocoaPods, create and add it in your `Podfile`:

```ruby
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '7.0'

target 'YourTargetName' do
pod 'BBLocationManager'
end
```

Then, run the following command:

```bash
$ pod install
```
And the pod should be installed in your project. PLEASE NOTE: Close the yourProject.xcodeProj and open the yourProject.xcworkspace, as the pod has been initiated, from now one use the yourProject.xcworkspace to work with. Please refer to [CocoaPods](http://cocoapods.org) for detailed info.

#### Manual Installation
Just add the BBLocationManager.h and BBLocationManager.m files in your project [From Here](https://github.com/benzamin/BBLocationManager/tree/master/BBLocationManager/BBLocationManagerClasses). Import the BBLocationManager.h file in your class where you need location support.


## Usage

`[BBLocationManager sharedManager]` creates an singleton class of `BBLocationManager` and manages everything from here. You can either use `BBLocationManagerDelegate` to get location/geofence related callbacks, or use Objective-C blocks to get location. For useing Geofence, the `BBFenceInfo` is a easy to use object, using which `BBLocationManager` delivers fence related data to your class. You can use `lastKnownGeocodeAddress` and `lastKnownGeoLocation` properties to get the last location/geocode the class got before.

#### Getting current location (Using Block)

```objective-c
BBLocationManager *manager = [BBLocationManager sharedManager];
    [manager getCurrentLocationWithCompletion:^(BOOL success, NSDictionary *latLongAltitudeDictionary, NSError *error) {
        //access the 'latLongAltitudeDictionary' dictionary using BB_LATITUDE, BB_LONGITUDE, BB_ALTITUDE key
        NSLog(@"Current Location latitude: %@", latLongAltitudeDictionary[BB_LATITUDE]);
    }];
```
#### Getting current location (Using Delegate)

```objective-c
BBLocationManager *manager = [BBLocationManager sharedManager];
[manager getCurrentLocationWithDelegate:self];  
......
......
#pragma mark - BBLocationManagerDelegate methods
-(void)BBLocationManagerDidUpdateLocation:(NSDictionary *)latLongAltitudeDictionary
{
    //access the 'latLongAltitudeDictionary' dictionary using BB_LATITUDE, BB_LONGITUDE, BB_ALTITUDE key
    NSLog(@"Current Location Latitude: %@", latLongAltitudeDictionary[BB_LATITUDE]);
}
```
#### Getting current geocode/address (Using Block)

```objective-c
BBLocationManager *manager = [BBLocationManager sharedManager];
    [manager getCurrentGeoCodeAddressWithCompletion:^(BOOL success, NSDictionary *addressDictionary, NSError *error) {
        //access the dict using BB_LATITUDE, BB_LONGITUDE, BB_ALTITUDE, BB_ADDRESS_NAME, BB_ADDRESS_STREET, BB_ADDRESS_CITY, BB_ADDRESS_STATE, BB_ADDRESS_COUNTY, BB_ADDRESS_ZIPCODE, BB_ADDRESS_COUNTY, BB_ADDRESS_DICTIONARY
        NSLog(NSString stringWithFormat:@"Current GeoCode/Address: %@", addressDictionary.description);
    }];
```

![Geofence and alert](https://raw.githubusercontent.com/benzamin/BBLocationManager/master/screens/geofence.gif  "Adding Geofence and getting alert")
![Geofence Alert](https://raw.githubusercontent.com/benzamin/BBLocationManager/master/screens/geofence-alert.gif  "Geofence enter/exit alert while app in background/exited")

#### Add a geofence in current location

```objective-c
BBLocationManager *manager = [BBLocationManager sharedManager];
manager.delegate = self;
[manager addGeofenceAtCurrentLocation];
/*****can be also done using one of the following methods *****/
//[manager addGeofenceAtCurrentLocationWithRadious:100];
//[manager addGeofenceAtlatitude:59.331981f andLongitude:18.068435f withRadious:100 withIdentifier:@"MyFence-1";
//[manager addGeofenceAtCurrentLocationWithRadious:100 withIdentifier:@"MyFence-1"];
//[manager addGeofenceAtCoordinates:CLLocationCoordinate2DMake(59.331981f, 18.068435f) withRadious:100 withIdentifier:@"MyFence-1"];
//[manager addGeofenceAtCoordinates:CLLocationCoordinate2DMake(59.331981f, 18.068435f) withRadious:100 withIdentifier:nil];//If you provide identifier name 'nil', BBLocationManager will automatically asign a identifier string
 ```
 
 #### Get all added geofences 

```objective-c
 BBLocationManager *manager = [BBLocationManager sharedManager];
    
    NSArray *geoFences = [manager getCurrentFences];
    NSString *allFencetxt = @"All fences: ";
    for (BBFenceInfo *geofence in geoFences)
    {
        NSString *txt = [NSString stringWithFormat:@"Geofence '%@' is Active at Coordinates: %@:%@ with %@ meter radious \n", geofence.fenceIDentifier, [geofence.fenceCoordinate objectForKey:BB_LATITUDE],[geofence.fenceCoordinate objectForKey:BB_LONGITUDE], [geofence.fenceCoordinate objectForKey:BB_RADIOUS]];
        NSLog(@"%@", txt);
    }
```

 #### Delete a spefific geofence

```objective-c
 BBLocationManager *manager = [BBLocationManager sharedManager];
[manager deleteGeoFenceWithIdentifier:@"My-Geofence-3"];
```

#### Get Continious location

```objective-c
BBLocationManager *manager = [BBLocationManager sharedManager];
[manager getContiniousLocationWithDelegate:self];
```
#### Get Significant location change

```objective-c
BBLocationManager *manager = [BBLocationManager sharedManager];
[manager getSingificantLocationChangeWithDelegate:self];
```
#### Stop getting all kind of location

```objective-c
BBLocationManager *manager = [BBLocationManager sharedManager];
[manager stopGettingLocation];
```
#### BBLocationManagerDelegate methods
```objective-c
-(void)BBLocationManagerDidAddFence:(BBFenceInfo *)fenceInfo;
-(void)BBLocationManagerDidFailedFence:(BBFenceInfo *)fenceInf
-(void)BBLocationManagerDidEnterFence:(BBFenceInfo *)fenceInfo
-(void)BBLocationManagerDidExitFence:(BBFenceInfo *)fenceInfo;
-(void)BBLocationManagerDidUpdateLocation:(NSDictionary *)latLongAltitudeDictionary;
-(void)BBLocationManagerDidUpdateGeocodeAdress:(NSDictionary *)addressDictionary;
```



 
## Built With

* [Dropwizard](http://www.dropwizard.io/1.0.2/docs/) - The web framework used
* [Maven](https://maven.apache.org/) - Dependency Management
* [ROME](https://rometools.github.io/rome/) - Used to generate RSS Feeds

## Contributing

Please read [CONTRIBUTING.md](https://gist.github.com/PurpleBooth/b24679402957c63ec426) for details on our code of conduct, and the process for submitting pull requests to us.

## Versioning

We use [SemVer](http://semver.org/) for versioning. For the versions available, see the [tags on this repository](https://github.com/your/project/tags). 

## Authors

* **Billie Thompson** - *Initial work* - [PurpleBooth](https://github.com/PurpleBooth)

See also the list of [contributors](https://github.com/your/project/contributors) who participated in this project.

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details

## Acknowledgments

* Hat tip to anyone who's code was used
* Inspiration
* etc

