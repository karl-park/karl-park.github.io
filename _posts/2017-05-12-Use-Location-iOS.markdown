---
layout: post
title:  "[iOS] iOS Location"
date:   2017-05-12 03:00:00
tags: objc ios Location CoreLocation
categories: devstory
---
본문에서는 iOS에서 위치 정보를 가져오는 방법을 설명한다.

# iOS 위치정보 이용하기 !

제일 먼저, CoreLocation 프레임워크를 프로젝트에 Add 및 import 시켜줘야한다. 해당 프레임워크에서 Location을 관리하기 때문이리라.

### 1. Add Framework
- `CoreLocation.framework`


### 2. #import <CoreLocation/CoreLocation.h>
```objc
#import <CoreLocation/CoreLocation.h>

@interface MyLocationManager : NSObject <CLLocationManagerDelegate>

...

@end

```

### 3. Implements `CLLocationManagerDelegate`
이제는 구현의 단계이다. CLLocationManagerDelegate의 다음 메소스들을 구현해주자.

```objc
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
모든 설정이 끝났다면, LocationManager를 이용하여 위치정보를 받아오자 !

```objc
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
"위치" 정보는 사실상 개인정보나 다름없다. 민감한 정보이기에 Info.plist에서 Privacy Description을 입력해주어야한다. (리젝을 피하자 !)

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
