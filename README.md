# EMAX Wi-FiDeviceConnector
Framework container **ConnectorManager class** and **customizable UI**

## Installation
As a CocoaPod, add this to your Podfile:


```
pod 'EMAX-Wi-FiDeviceConnector'

```

## Usage

### ConnectorManager

#####How to use

see `StepThreeViewController.m`


### UI
```objective-c
#import "StepOneViewController.h"

- (IBAction)didClickBtn {
    ConnectorCustomizer *customizer = [ConnectorCustomizer new];
    customizer.tintColor = [UIColor orangeColor];
    customizer.deviceSettingGuide = [UIImage imageNamed:@"deviceSettingGuide_cn"];
    customizer.wifiSettingGuide = [UIImage imageNamed:@"wifiSettingGuide"];
    customizer.successBlock = ^(UIViewController *vc, NSString *deviceMAC) {
        [vc.navigationController pushViewController:[SuccessViewController new] animated:YES];
    };
    
    StepOneViewController *vc = [StepOneViewController new];
    vc.customizer = customizer;
    // Same background color in the next three ViewController
    vc.view.backgroundColor = [UIColor whiteColor];
    [self.navigationController pushViewController:vc animated:YES];
}

```

## ConnectorManager.h
```objective-c
typedef enum : NSUInteger {
    TagMean_Init, // including bind host&port, test connection
    TagMean_ShouldScan, // tell device scan Wi-Fi
    TagMean_ShouldSetPIN, // including PIN, auth, encry
    TagMean_ShouldSetSSID,
    TagMean_Succeed,
} TagMean;

@interface ConnectorManager : NSObject

@property (nonatomic, copy) NSString *host;
@property (nonatomic, assign) uint16_t port;
- (instancetype)initWithHost:(NSString *)host port:(uint16_t)port;

/**
 开始为设备设置Wi-Fi连接 
 begin setting device's Wi-Fi connection
 
 @param ssid Wi-Fi名
 @param pin Wi-Fi密码
 @param block
    isSuccess
        上一个任务是否成功（TagMean_Succeed除外）
        indicate whether previous action is succuss
    tagMean
        接下来的任务
        next action
 NOTE:
    当(tag == TagMean_Succeed && isSuccess == YES)时，表示本次任务完成
    The task succeed when (tag == TagMean_Succeed && isSuccess == YES).
 */
- (void)beginConnectTaskWithSSID:(NSString *)ssid
                             pin:(NSString *)pin
                     finishBlock:(void (^)(BOOL isSuccess, TagMean tagMean))block;

/**
 设备mac地址(与设备初始化连接后才有值)
 device's mac(not nil after 'beginConnectTaskWithSSID')
 */
@property (nonatomic, copy, readonly) NSString *deviceMAC;

/**
 手机当前连接的Wi-Fi名称
 current Wi-Fi SSID of iPhone connected
 */
+ (NSString *)currentSSID;

@end

```

## ConnectorCustomizer.h
```objective-c
@interface ConnectorCustomizer : NSObject

/**
 default is blackColor */
@property (nonatomic, strong) UIColor *tintColor;
/**
 default is blackColor */
@property (nonatomic, strong) UIColor *textColor;
/**
 default is whiteColor */
@property (nonatomic, strong) UIColor *btnTextColor;


/**
 指导用户开启设备AP模式的图片（在步骤2中展示）
 The image guide shows in step 2, which is to guide users how to turn on device AP mode.
 */
@property (nonatomic, strong) UIImage *deviceSettingGuide;
/**
 指导用户连接设备Wi-Fi的图片（在步骤3中展示）
 The guiding image shows in step 3, which is to guide users connect to device's Wi-Fi.
 */
@property (nonatomic, strong) UIImage *wifiSettingGuide;

/**
 default is "LivingSmart" */
@property (nonatomic, copy) NSString *deviceSSID;
/**
 default is "11.11.11.254" */
@property (nonatomic, copy) NSString *host;
/**
 default is 8800 */
@property (nonatomic, assign) uint16_t port;

/**
 All done block */
@property (nonatomic, copy) void(^successBlock)(UIViewController *vc, NSString *deviceMAC);

@end

```
