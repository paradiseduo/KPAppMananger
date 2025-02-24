## [项目地址](https://github.com/LZRight123/KPAppMananger)

## 功能
- 静默安装APP，卸载，打开，监听。
- 进程间通信。
- 获取设备UDID。
- 后台保活。

## 集成
- 直接使用
```sh
pod 'KPAppMananger', :git => 'https://github.com/LZRight123/KPAppMananger.git'
```
- clone到本地
```sh
git clone https://github.com/LZRight123/KPAppMananger.git
pod 'KPAppMananger', :path => '../KPAppMananger'
```
<!-- ## 配置
- copy script文件夹到项目目录
- `BuildPhases` -> `Run Script`
- 修改`test.sh`和`makefile`中的 `TARGET_NAME="AutoDownload"` 重要
```sh
sh script/test.sh
``` -->

## 用法
- 获取设备UDID
```sh
[KPAppTool UDID]
// 获取设备信息，包括UDID 本机名称 机身颜色 等
[KPAppTool deviceInfo]
```
- 后台保活
Signing & Capabilities -> BackgroundModes -> `Audio` 
```c
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    [[KPBackgroundTaskUtils sharedInstance] playNoVoliceBackgroundTask];
    return YES;
}

- (void)applicationDidEnterBackground:(UIApplication *)application {
    [[KPBackgroundTaskUtils sharedInstance] applicationDidEnterBackgroundTask:application];
}
```

- 操作app
```c
/// 安装本地
[KPAppTool installAppWithIpaPath:self.ipaPath appIdentfier:nil block:^(NSString *installDesc, NSString *completionDesc) {
    //... 
}];

/// url
[KPAppTool installAppWithRemoteURL:@"your ipa url" downloadProgress:nil completion:nil];


/// 卸载
[KPAppTool uninstallApplication:nil options:nil];

/// 打开
[KPAppTool openApplicationWithBundleID:nil];
```

- IPC通知
```c
/// 服务端：ping
[KPAppToolInstance pingTimeSecond:3];
[KPAppToolInstance registerIPCNotfication:@"kp.xm.qw.pong" callBack:^(NSString *name) {
    //....
}];
/// 客户端
[KPAppToolInstance recevicePingCallBack:^(NSString *name) {
    NSLog(@"接收到了ping: %@", name);
    [KPAppToolInstance postIPCNoftication:@"kp.xm.qw.pong"];
}];
```

- 进程间收发消息
```c
 [KPLocalConnect registerLisenerAtPort:5555 callBack:^NSDictionary * _Nonnull(NSString * _Nonnull path, NSDictionary * _Nonnull query) {
     if ([path isEqualToString:@"/xxxpath"]) {
         return @{
             @"path": path?:@"",
             @"quey": query?:@{},
         };
     }
     return @{
         @"msg": @"path is error",
     };
 }];
 
 /// 客户端
 [KPLocalConnect getAtPort:5555 path:@"xxxpath" parameters:@{@"key1": @"value"} completion:^(NSDictionary * _Nonnull response, NSError * _Nonnull error, NSURLSessionDataTask * _Nonnull task) {
     NSLog(@"response: %@", response);;
 }];
```
