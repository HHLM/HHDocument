## IOS高级开发～开机启动&无限后台运行&监听进程

> 1、开机启动：App安装到IOS设备设备之后，无论App是否开启过，只要IOS设备重启，App就会随之启动；
> 2、无限后台运行：应用进入后台状态，可以无限后台运行，不被系统kill；
> 3、监听进程：可获IOS设备运行除系统外的App（包括正在运行和后台运行）；

```
配置项目 plist文件
添加：
<key>UIBackgroundModes</key>
<array>
<string>voip</string>
</array>

功能类：ProccessHelper
```
```objc
#import <Foundation/Foundation.h>  
  
@interface ProccessHelper : NSObject  
  
+ (NSArray *)runningProcesses;  
  
@end  

[cpp] view plaincopyprint?
#import "ProccessHelper.h"  
//#include<objc/runtime.h>  
#include <sys/sysctl.h>  
  
#include <stdbool.h>  
#include <sys/types.h>  
#include <unistd.h>  
#include <sys/sysctl.h>  
  
@implementation ProccessHelper  
  
//You can determine if your app is being run under the debugger with the following code from  
static bool AmIBeingDebugged(void)  
// Returns true if the current process is being debugged (either  
// running under the debugger or has a debugger attached post facto).  
{  
    int                 junk;  
    int                 mib[4];  
    struct kinfo_proc   info;  
    size_t              size;  
      
    // Initialize the flags so that, if sysctl fails for some bizarre  
    // reason, we get a predictable result.  
      
    info.kp_proc.p_flag = 0;  
      
    // Initialize mib, which tells sysctl the info we want, in this case  
    // we're looking for information about a specific process ID.  
      
    mib[0] = CTL_KERN;  
    mib[1] = KERN_PROC;  
    mib[2] = KERN_PROC_PID;  
    mib[3] = getpid();  
      
    // Call sysctl.  
      
    size = sizeof(info);  
    junk = sysctl(mib, sizeof(mib) / sizeof(*mib), &info, &size, NULL, 0);  
    assert(junk == 0);  
      
    // We're being debugged if the P_TRACED flag is set.  
      
    return ( (info.kp_proc.p_flag & P_TRACED) != 0 );  
}  
  
//返回所有正在运行的进程的 id，name，占用cpu，运行时间  
//使用函数int   sysctl(int *, u_int, void *, size_t *, void *, size_t)  
+ (NSArray *)runningProcesses  
{  
    //指定名字参数，按照顺序第一个元素指定本请求定向到内核的哪个子系统，第二个及其后元素依次细化指定该系统的某个部分。  
    //CTL_KERN，KERN_PROC,KERN_PROC_ALL 正在运行的所有进程  
    int mib[4] = {CTL_KERN, KERN_PROC, KERN_PROC_ALL ,0};  
      
      
    size_t miblen = 4;  
    //值-结果参数：函数被调用时，size指向的值指定该缓冲区的大小；函数返回时，该值给出内核存放在该缓冲区中的数据量  
    //如果这个缓冲不够大，函数就返回ENOMEM错误  
    size_t size;  
    //返回0，成功；返回-1，失败  
    int st = sysctl(mib, miblen, NULL, &size, NULL, 0);  
      
    struct kinfo_proc * process = NULL;  
    struct kinfo_proc * newprocess = NULL;  
    do  
    {  
        size += size / 10;  
        newprocess = realloc(process, size);  
        if (!newprocess)  
        {  
            if (process)  
            {  
                free(process);  
                process = NULL;  
            }  
            return nil;  
        }  
          
        process = newprocess;  
        st = sysctl(mib, miblen, process, &size, NULL, 0);  
    } while (st == -1 && errno == ENOMEM);  
      
    if (st == 0)  
    {  
        if (size % sizeof(struct kinfo_proc) == 0)  
        {  
            int nprocess = size / sizeof(struct kinfo_proc);  
            if (nprocess)  
            {  
                NSMutableArray * array = [[NSMutableArray alloc] init];  
                for (int i = nprocess - 1; i >= 0; i--)  
                {  
                    NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];  
                    NSString * processID = [[NSString alloc] initWithFormat:@"%d", process[i].kp_proc.p_pid];  
                    NSString * processName = [[NSString alloc] initWithFormat:@"%s", process[i].kp_proc.p_comm];  
                    NSString * proc_CPU = [[NSString alloc] initWithFormat:@"%d", process[i].kp_proc.p_estcpu];  
                    double t = [[NSDate date] timeIntervalSince1970] - process[i].kp_proc.p_un.__p_starttime.tv_sec;  
                    NSString * proc_useTiem = [[NSString alloc] initWithFormat:@"%f",t];  
                    NSString *startTime = [[NSString alloc] initWithFormat:@"%ld", process[i].kp_proc.p_un.__p_starttime.tv_sec];  
                    NSString * status = [[NSString alloc] initWithFormat:@"%d",process[i].kp_proc.p_flag];  
                      
                    NSMutableDictionary *dic = [[NSMutableDictionary alloc] init];  
                    [dic setValue:processID forKey:@"ProcessID"];  
                    [dic setValue:processName forKey:@"ProcessName"];  
                    [dic setValue:proc_CPU forKey:@"ProcessCPU"];  
                    [dic setValue:proc_useTiem forKey:@"ProcessUseTime"];  
                    [dic setValue:proc_useTiem forKey:@"ProcessUseTime"];  
                    [dic setValue:startTime forKey:@"startTime"];  
                      
                    // 18432 is the currently running application  
                    // 16384 is background  
                    [dic setValue:status forKey:@"status"];  
                      
                    [processID release];  
                    [processName release];  
                    [proc_CPU release];  
                    [proc_useTiem release];  
                    [array addObject:dic];  
                    [startTime release];  
                    [status release];  
                    [dic release];  
                      
                    [pool release];  
                }  
                  
                free(process);  
                process = NULL;  
                //NSLog(@"array = %@",array);  
                  
                return array;  
            }  
        }  
    }  
      
    return nil;  
}  
  
@end  
```

```objc
systemprocessArray = [[NSMutableArray arrayWithObjects:  
                                @"kernel_task",  
                                @"launchd",  
                                @"UserEventAgent",  
                                @"wifid",  
                                @"syslogd",  
                                @"powerd",  
                                @"lockdownd",  
                                @"mediaserverd",  
                                @"mediaremoted",  
                                @"mDNSResponder",  
                                @"locationd",  
                                @"imagent",  
                                @"iapd",  
                                @"fseventsd",  
                                @"fairplayd.N81",  
                                @"configd",  
                                @"apsd",  
                                @"aggregated",  
                                @"SpringBoard",  
                                @"CommCenterClassi",  
                                @"BTServer",  
                                @"notifyd",  
                                @"MobilePhone",  
                                @"ptpd",  
                                @"afcd",  
                                @"notification_pro",  
                                @"notification_pro",  
                                @"syslog_relay",  
                                @"notification_pro",  
                                @"springboardservi",  
                                @"atc",  
                                @"sandboxd",  
                                @"networkd",  
                                @"lsd",  
                                @"securityd",  
                                @"lockbot",  
                                @"installd",  
                                @"debugserver",  
                                @"amfid",  
                                @"AppleIDAuthAgent",  
                                @"BootLaunch",  
                                @"MobileMail",  
                                @"BlueTool",  
                                nil] retain];  

```

```objc
- (void)applicationDidEnterBackground:(UIApplication *)application  
{  
    while (1) {  
        sleep(5);  
        [self postMsg];  
    }  
      
[cpp] view plaincopyprint?
    [[UIApplication sharedApplication] setKeepAliveTimeout:600 handler:^{  
        NSLog(@"KeepAlive");  
    }];  
}  
  
- (void)applicationWillResignActive:(UIApplication *)application  
{  
}  
- (void)applicationWillEnterForeground:(UIApplication *)application  
{  
}  
- (void)applicationDidBecomeActive:(UIApplication *)application  
{  
}  
- (void)applicationWillTerminate:(UIApplication *)application  
{  
}  
  
#pragma mark -  
#pragma mark - User Method  
  
- (void) postMsg  
{  
    //上传到服务器  
    NSURL *url = [self getURL];  
    NSURLRequest *request = [[NSURLRequest alloc]initWithURL:url cachePolicy:NSURLRequestUseProtocolCachePolicy timeoutInterval:10];  
    NSError *error = nil;  
    NSData *received = [NSURLConnection sendSynchronousRequest:request returningResponse:nil error:&error];  
      
    if (error) {  
        NSLog(@"error:%@", [error localizedDescription]);  
    }  
      
    NSString *str = [[NSString alloc]initWithData:received encoding:NSUTF8StringEncoding];  
    NSLog(@"%@",str);  
}  
  
- (NSURL *) getURL  
{  
    UIDevice *device = [UIDevice currentDevice];  
      
    NSString* uuid = @"TESTUUID";  
    NSString* manufacturer = @"apple";  
    NSString* model = [device model];  
    NSString* mobile = [device systemVersion];  
      
    NSString *msg = [NSString stringWithFormat:@"Msg:%@  Time:%@", [self processMsg], [self getTime]];  
    CFShow(msg);  
      
    /  省略部分代码  /  
      
    NSString *urlStr = [strUrl stringByAddingPercentEscapesUsingEncoding:NSUTF8StringEncoding];  
    NSURL *url = [NSURL URLWithString:urlStr];  
      
    return url;  
}  
  
- (BOOL) checkSystemProccess:(NSString *) proName  
{  
    if ([systemprocessArray containsObject:proName]) {  
        return YES;  
    }  
    return NO;  
}  
  
- (BOOL) checkFirst:(NSString *) string  
{  
    NSString *str = [string substringToIndex:1];  
    NSRange r = [@"ABCDEFGHIJKLMNOPQRSTUVWXWZ" rangeOfString:str];  
      
    if (r.length > 0) {  
        return YES;  
    }  
    return NO;  
}  
  
- (NSString *) processMsg  
{  
    NSArray *proMsg = [ProccessHelper runningProcesses];  
  
    if (proMsg == nil) {  
        return nil;  
    }  
      
    NSMutableArray *proState = [NSMutableArray array];  
    for (NSDictionary *dic in proMsg) {  
          
        NSString *proName = [dic objectForKey:@"ProcessName"];  
        if (![self checkSystemProccess:proName] && [self checkFirst:proName]) {  
            NSString *proID = [dic objectForKey:@"ProcessID"];  
            NSString *proStartTime = [dic objectForKey:@"startTime"];  
              
            if ([[dic objectForKey:@"status"] isEqualToString:@"18432"]) {  
                NSString *msg = [NSString stringWithFormat:@"ProcessName:%@ - ProcessID:%@ - StartTime:%@ Running:YES", proName, proID, proStartTime];  
                [proState addObject:msg];  
            } else {  
                NSString *msg = [NSString stringWithFormat:@"ProcessName:%@ - ProcessID:%@ - StartTime:%@ Running:NO", proName, proID, proStartTime];  
                [proState addObject:msg];  
            }  
        }  
    }  
      
    NSString *msg = [proState componentsJoinedByString:@"______"];  
    return msg;  
}  
  
// 获取时间  
- (NSString *) getTime  
{  
    NSDateFormatter *formatter =[[[NSDateFormatter alloc] init] autorelease];  
    formatter.dateStyle = NSDateFormatterMediumStyle;  
    formatter.timeStyle = NSDateFormatterMediumStyle;  
    formatter.locale = [NSLocale currentLocale];  
      
    NSDate *date = [NSDate date];  
      
    [formatter setTimeStyle:NSDateFormatterMediumStyle];  
    NSCalendar *calendar = [[[NSCalendar alloc] initWithCalendarIdentifier:NSGregorianCalendar] autorelease];  
    NSDateComponents *comps = [[[NSDateComponents alloc] init] autorelease];  
    NSInteger unitFlags = NSYearCalendarUnit |  
    NSMonthCalendarUnit |  
    NSDayCalendarUnit |  
    NSWeekdayCalendarUnit |  
    NSHourCalendarUnit |  
    NSMinuteCalendarUnit |  
    NSSecondCalendarUnit;  
    comps = [calendar components:unitFlags fromDate:date];  
    int year = [comps year];  
    int month = [comps month];  
    int day = [comps day];  
    int hour = [comps hour];  
    int min = [comps minute];  
    int sec = [comps second];  
      
    NSString *time = [NSString stringWithFormat:@"%d-%d-%d %d:%d:%d", year, month, day, hour, min, sec];  
     
    return time;  
}  
  
@end  
```


