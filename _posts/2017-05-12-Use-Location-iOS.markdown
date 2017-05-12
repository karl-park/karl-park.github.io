---
layout: post
title:  "[iOS] Location"
date:   2016-05-24 03:00:00
categories: [iOS]
tags: [iOS,Objective-C,CoreLocation,Location]
---

# iOS 위치정보 이용하기 !

### 1. Add Framework
- `CoreLocation.framework`

### 2. #import <CoreLocation/CoreLocation.h>
```objectivec
#import <CoreLocation/CoreLocation.h>

@interface MyLocationManager : NSObject <CLLocationManagerDelegate>

...

@end

```


### 3. Implements `CLLocationManagerDelegate`
```objectivec
- (void)locationManager:(CLLocationManager *)manager didUpdateLocations:(NSArray<CLLocation *> *)locations {
    NSLog(@"Locations : %@", locations);
    NSLog(@"CLLocationManager : %@", manager);
}

- (void)locationManager:(CLLocationManager *)manager didFailWithError:(NSError *)error {
    NSLog(@"error : %@", error);
    NSLog(@"CLLocationManager : %@", manager);
}

...
```


### 4. LocationManager Allocation and Start
```objectivec
typedef NS_ENUM(NSUInteger, PermissionTypeForLocation) {
    PermissionTypeForLocationRequest,
    PermissionTypeForLocationAlways,
    PermissionTypeForLocationUseWhenInUsage,
};

@interface MyLocationManager 
@property (nonatomic, strong) locationManager;
...
@end


@implementation MyLocationManager
@synthesize locationManager = _locationManager;
...
- (void)initializeLocationManager {
    self.locationManager = [[CLLocationManager alloc] init];
    [self.locationManager setDelegate:self];
}


- (void)requestLocationPermissionWithType:(PermissionTypeForLocation)type {
    switch (type) {
        case PermissionTypeForLocationRequest : {
            [_locationmanager requestLocation];
            break;
        }
        case PermissionTypeForLocationAlways : {
            [_locationmanager requestAlwaysAuthorization];
            break;
        }
        case PermissionTypeForLocationUseWhenInUsage : {
            [_locationmanager requestWhenInUseAuthorization];
            break;
        }
        default: {
            NSLog(@"PermissionTypeForLocation is not valid type.");
        }
    }
}

- (void)startUpdatingLocation {
    [self.locationManager startUpdatingLocation];
}
```



### 5. Add Privacy Description to Info.plist
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>NSLocationUsageDescription</key>
	<string>위치 정보를 사용할게요.</string>
	<key>NSLocationAlwaysUsageDescription</key>
	<string>항상 위치정보를 사용합니다.</string>
	<key>NSLocationWhenInUseUsageDescription</key>
	<string>사용중에만 위치정보를 사용하겠습니다.</string>
</dict>
</plist>    
```