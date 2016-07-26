#iOS小技巧汇总

* 1 打印view所有子视图

```objc
po [[self view]recursiveDescription]
```

* 2. layoutSubviews调用的调用时机

```objc
 * 当视图第一次显示的时候会被调用
 * 当这个视图显示到屏幕上了,点击按钮
 * 添加子视图也会调用这个方法
 * 当本视图的大小发生改变的时候是会调用的
 * 当子视图的frame发生改变的时候是会调用的
 * 当删除子视图的时候是会调用的
```

* 3 NSString过滤特殊字符

```objc
// 定义一个特殊字符的集合 NSCharacterSet *set = [NSCharacterSet characterSetWithCharactersInString: @"@／：；（）¥「」＂、[]{}#%-*+=_\\|~＜＞$€^•'@#$%^&*()_+'\""]; // 过滤字符串的特殊字符 NSString *newString = [trimString stringByTrimmingCharactersInSet:set];
```

* 4 TransForm属性

```objc
//平移按钮 CGAffineTransform transForm = self.buttonView.transform; self.buttonView.transform = CGAffineTransformTranslate(transForm, 10, 0);  //旋转按钮 CGAffineTransform transForm = self.buttonView.transform; self.buttonView.transform = CGAffineTransformRotate(transForm, M_PI_4);  //缩放按钮 self.buttonView.transform = CGAffineTransformScale(transForm, 1.2, 1.2);  //初始化复位 self.buttonView.transform = CGAffineTransformIdentity;
```

* 5. 去掉分割线多余15像素

```objc
首先在viewDidLoad方法加入以下代码： if ([self.tableView respondsToSelector:@selector(setSeparatorInset:)]) { [self.tableView setSeparatorInset:UIEdgeInsetsZero]; } if ([self.tableView respondsToSelector:@selector(setLayoutMargins:)]) { [self.tableView setLayoutMargins:UIEdgeInsetsZero]; } 然后在重写willDisplayCell方法 - (void)tableView:(UITableView *)tableView willDisplayCell:(UITableViewCell *)cell forRowAtIndexPath:(NSIndexPath *)indexPath{ if ([cell respondsToSelector:@selector(setSeparatorInset:)]) { [cell setSeparatorInset:UIEdgeInsetsZero]; } if ([cell respondsToSelector:@selector(setLayoutMargins:)]) { [cell setLayoutMargins:UIEdgeInsetsZero]; } }
```

* 6. 计算方法耗时时间间隔

```objc
// 获取时间间隔 #define TICK   CFAbsoluteTime start = CFAbsoluteTimeGetCurrent(); #define TOCK   NSLog(@"Time: %f", CFAbsoluteTimeGetCurrent() - start)
```

* 7. Color颜色宏定义

```objc
// 随机颜色 #define RANDOM_COLOR [UIColor colorWithRed:arc4random_uniform(256) / 255.0 green:arc4random_uniform(256) / 255.0 blue:arc4random_uniform(256) / 255.0 alpha:1] // 颜色(RGB) #define RGBCOLOR(r, g, b) [UIColor colorWithRed:(r)/255.0f green:(g)/255.0f blue:(b)/255.0f alpha:1] // 利用这种方法设置颜色和透明值，可不影响子视图背景色 #define RGBACOLOR(r, g, b, a) [UIColor colorWithRed:(r)/255.0f green:(g)/255.0f blue:(b)/255.0f alpha:(a)]
```

* 8. Alert提示宏定义

```objc
#define Alert(_S_, ...) [[[UIAlertView alloc] initWithTitle:@"提示" message:[NSString stringWithFormat:(_S_), ##__VA_ARGS__] delegate:nil cancelButtonTitle:@"确定" otherButtonTitles:nil] show]
```

* 9. 让iOS应用直接退出

```objc
- (void)exitApplication { AppDelegate *app = [UIApplication sharedApplication].delegate; UIWindow *window = app.window; [UIView animateWithDuration:1.0f animations:^{ window.alpha = 0; } completion:^(BOOL finished) { exit(0); }]; }
```

* 10. 修改Label中不同文字颜色

```objc
- (void)touchesEnded:(NSSet<UITouch> *)touches withEvent:(UIEvent *)event{    [self editStringColor:self.label.text editStr:@"好" color:[UIColor blueColor]];} - (void)editStringColor:(NSString *)string editStr:(NSString *)editStr color:(UIColor *)color {    // string为整体字符串, editStr为需要修改的字符串    NSRange range = [string rangeOfString:editStr];     NSMutableAttributedString *attribute = [[NSMutableAttributedString alloc] initWithString:string];     // 设置属性修改字体颜色UIColor与大小UIFont    [attribute addAttributes:@{NSForegroundColorAttributeName:color} range:range];     self.label.attributedText = attribute;}
```

* 11. NSArray 快速求总和 最大值 最小值 和 平均值

```objc
NSArray *array = [NSArray arrayWithObjects:@"2.0", @"2.3", @"3.0", @"4.0", @"10", nil];CGFloat sum = [[array valueForKeyPath:@"@sum.floatValue"] floatValue];CGFloat avg = [[array valueForKeyPath:@"@avg.floatValue"] floatValue];CGFloat max =[[array valueForKeyPath:@"@max.floatValue"] floatValue];CGFloat min =[[array valueForKeyPath:@"@min.floatValue"] floatValue];NSLog(@"%f\n%f\n%f\n%f",sum,avg,max,min);
```

* 11.播放声音

```objc
#import<AVFoundation> //  1.获取音效资源的路径 NSString *path = [[NSBundle mainBundle]pathForResource:@"pour_milk" ofType:@"wav"]; //  2.将路劲转化为url NSURL *tempUrl = [NSURL fileURLWithPath:path]; //  3.用转化成的url创建一个播放器 NSError *error = nil; AVAudioPlayer *play = [[AVAudioPlayer alloc]initWithContentsOfURL:tempUrl error:&error]; self.player = play; //  4.播放 [play play];
```


* 12. UILabel行间距

```objc
-（void）test{    NSMutableAttributedString *attributedString =       [[NSMutableAttributedString alloc] initWithString:self.contentLabel.text];    NSMutableParagraphStyle *paragraphStyle =  [[NSMutableParagraphStyle alloc] init];     [paragraphStyle setLineSpacing:3];     //调整行间距          [attributedString addAttribute:NSParagraphStyleAttributeName                         value:paragraphStyle                          range:NSMakeRange(0, [self.contentLabel.text length])];     self.contentLabel.attributedText = attributedString;}
```

* 13.UIImageView填充模式

```objc
@"UIViewContentModeScaleToFill",      // 拉伸自适应填满整个视图  @"UIViewContentModeScaleAspectFit",   // 自适应比例大小显示  @"UIViewContentModeScaleAspectFill",  // 原始大小显示  @"UIViewContentModeRedraw",           // 尺寸改变时重绘  @"UIViewContentModeCenter",           // 中间  @"UIViewContentModeTop",              // 顶部  @"UIViewContentModeBottom",           // 底部  @"UIViewContentModeLeft",             // 中间贴左  @"UIViewContentModeRight",            // 中间贴右  @"UIViewContentModeTopLeft",          // 贴左上  @"UIViewContentModeTopRight",         // 贴右上  @"UIViewContentModeBottomLeft",       // 贴左下  @"UIViewContentModeBottomRight",      // 贴右下
```

* 14.检测block是否可用

```objc
#define BLOCK_EXEC(block, ...) if (block) { block(__VA_ARGS__); };   // 宏定义之前的用法 if (completionBlock)   {       completionBlock(arg1, arg2);   }    // 宏定义之后的用法 BLOCK_EXEC(completionBlock, arg1, arg2);
```

* 15.Debug栏打印时自动把Unicode编码转化成汉字

```objc
// 有时候我们在xcode中打印中文,会打印出Unicode编码,还需要自己去一些在线网站转换,有了插件就方便多了。 DXXcodeConsoleUnicodePlugin 插件
```

* 16.设置状态栏文字样式颜色

```objc
[[UIApplication sharedApplication] setStatusBarHidden:NO];[[UIApplication sharedApplication] setStatusBarStyle:UIStatusBarStyleLightContent];
```

* 17.自动生成模型代码的插件

```objc
// 可自动生成模型的代码，省去写模型代码的时间ESJsonFormat-for-Xcode
```

* 18.iOS 开发中一些相关的路径

```objc
模拟器的位置:/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer/SDKs  文档安装位置:/Applications/Xcode.app/Contents/Developer/Documentation/DocSets 插件保存路径:~/Library/ApplicationSupport/Developer/Shared/Xcode/Plug-ins 自定义代码段的保存路径:~/Library/Developer/Xcode/UserData/CodeSnippets/ 如果找不到CodeSnippets文件夹，可以自己新建一个CodeSnippets文件夹。 证书路径~/Library/MobileDevice/Provisioning Profiles
```

* 19.获取 iOS 路径的方法

```objc
获取家目录路径的函数NSString *homeDir = NSHomeDirectory(); 获取Documents目录路径的方法NSArray *paths = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES);NSString *docDir = [paths objectAtIndex:0]; 获取Documents目录路径的方法NSArray *paths = NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES);NSString *cachesDir = [paths objectAtIndex:0]; 获取tmp目录路径的方法：NSString *tmpDir = NSTemporaryDirectory();
```

* 20.字符串相关操作

```objc
去除所有的空格[str stringByReplacingOccurrencesOfString:@" " withString:@""] 去除首尾的空格[str stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceCharacterSet]]; - (NSString *)uppercaseString; 全部字符转为大写字母- (NSString *)lowercaseString 全部字符转为小写字母
```

* 21. 设置滑动的时候隐藏navigationbar

```objc
navigationController.hidesBarsOnSwipe = YES
```

* 22. 把tableview里cell的小对勾的颜色改成别的颜色

```objc
_mTableView.tintColor = [UIColor redColor];
```

* 23.Quartz2D相关

```objc
图形上下是一个CGContextRef类型的数据。图形上下文包含：1，绘图路径（各种各样图形）2，绘图状态（颜色，线宽，样式，旋转，缩放，平移）3，输出目标（绘制到什么地方去？UIView、图片） 1，获取当前图形上下文CGContextRef ctx = UIGraphicsGetCurrentContext();2，添加线条CGContextMoveToPoint(ctx, 20, 20);3，渲染CGContextStrokePath(ctx);CGContextFillPath(ctx);4，关闭路径CGContextClosePath(ctx);5，画矩形CGContextAddRect(ctx, CGRectMake(20, 20, 100, 120));6，设置线条颜色[[UIColor redColor] setStroke];7， 设置线条宽度CGContextSetLineWidth(ctx, 20);8，设置头尾样式CGContextSetLineCap(ctx, kCGLineCapSquare);9，设置转折点样式CGContextSetLineJoin(ctx, kCGLineJoinBevel);10，画圆CGContextAddEllipseInRect(ctx, CGRectMake(30, 50, 100, 100));11，指定圆心CGContextAddArc(ctx, 100, 100, 50, 0, M_PI * 2, 1);12，获取图片上下文UIGraphicsGetImageFromCurrentImageContext();13，保存图形上下文CGContextSaveGState(ctx)14，恢复图形上下文CGContextRestoreGState(ctx)
```

* 24.屏幕截图

```objc
// 1. 开启一个与图片相关的图形上下文    UIGraphicsBeginImageContextWithOptions(self.view.bounds.size,NO,0.0);     // 2. 获取当前图形上下文    CGContextRef ctx = UIGraphicsGetCurrentContext();     // 3. 获取需要截取的view的layer    [self.view.layer renderInContext:ctx];     // 4. 从当前上下文中获取图片    UIImage *image = UIGraphicsGetImageFromCurrentImageContext();     // 5. 关闭图形上下文    UIGraphicsEndImageContext();     // 6. 把图片保存到相册    UIImageWriteToSavedPhotosAlbum(image, nil, nil, nil);
```

* 25.隐藏导航栏上的返回字体

```objc
//SwiftUIBarButtonItem.appearance().setBackButtonTitlePositionAdjustment(UIOffsetMake(0, -60), forBarMetrics: .Default)//OC[[UIBarButtonItem appearance] setBackButtonTitlePositionAdjustment:UIOffsetMake(0, -60) forBarMetrics:UIBarMetricsDefault];
```

* 26.动态隐藏NavigationBar

```objc
//1.当我们的手离开屏幕时候隐藏- (void)scrollViewWillEndDragging:(UIScrollView *)scrollView withVelocity:(CGPoint)velocity targetContentOffset:(inout CGPoint *)targetContentOffset{ if(velocity.y > 0) {[self.navigationController setNavigationBarHidden:YES animated:YES];} else {[self.navigationController setNavigationBarHidden:NO animated:YES]; }}velocity.y这个量，在上滑和下滑时，变化极小（小数），但是因为方向不同，有正负之分，这就很好处理了。//2.在滑动过程中隐藏//像safari(1) self.navigationController.hidesBarsOnSwipe = YES;(2)- (void)scrollViewDidScroll:(UIScrollView *)scrollView{ CGFloat offsetY = scrollView.contentOffset.y + __tableView.contentInset.top; CGFloat panTranslationY = [scrollView.panGestureRecognizer translationInView:self.tableView].y; if (offsetY > 64) { if (panTranslationY > 0) { //下滑趋势，显示 [self.navigationController setNavigationBarHidden:NO animated:YES];} else { //上滑趋势，隐藏 [self.navigationController setNavigationBarHidden:YES animated:YES]; }} else {[self.navigationController setNavigationBarHidden:NO animated:YES]; }}这里的offsetY > 64只是为了在视图滑过navigationBar的高度之后才开始处理，防止影响展示效果。panTranslationY是scrollView的pan手势的手指位置的y值，可能不是太好，因为panTranslationY这个值在较小幅度上下滑动时，可能都为正或都为负，这就使得这一方式不太灵敏.
```
![效果图](http://7xstl7.com1.z0.glb.clouddn.com/%E6%8A%80%E6%9C%AF/nav_hide.gif)

* 27.设置导航栏透明

```objc
//方法一:设置透明度[[[self.navigationController.navigationBar subviews]objectAtIndex:0] setAlpha:0.1];//方法二:设置背景图片/** * 设置导航栏,使其透明 **/- (void)setNavigationBarColor:(UIColor *)color targetController:(UIViewController *)targetViewController{//导航条的颜色 以及隐藏导航条的颜色targetViewController.navigationController.navigationBar.shadowImage = [[UIImage alloc]init]; CGRect rect=CGRectMake(0.0f, 0.0f, 1.0f, 1.0f); UIGraphicsBeginImageContext(rect.size);CGContextRef context = UIGraphicsGetCurrentContext(); CGContextSetFillColorWithColor(context, [color CGColor]); CGContextFillRect(context, rect); UIImage *theImage = UIGraphicsGetImageFromCurrentImageContext(); UIGraphicsEndImageContext(); [targetViewController.navigationController.navigationBar setBackgroundImage:theImage forBarMetrics:UIBarMetricsDefault];}
```

* 29.设置字体和行间距

```objc
//设置字体和行间距 UILabel * lable = [[UILabel alloc]initWithFrame:CGRectMake(50, 100, 300, 200)]; lable.text = @"大家好,我是Frank_chun,在这里我们一起学习新的知识,总结我们遇到的那些坑,共同的学习,共同的进步,共同的努力,只为美好的明天!!!有问题一起相互的探讨--438637472!!!"; lable.numberOfLines = 0;lable.font = [UIFont systemFontOfSize:12];lable.backgroundColor = [UIColor grayColor]; [self.view addSubview:lable]; //设置每个字体之间的间距 //NSKernAttributeName 这个对象所对应的值是一个NSNumber对象(包含小数),作用是修改默认字体之间的距离调整,值为0的话表示字距调整是禁用的; NSMutableAttributedString * str = [[NSMutableAttributedString alloc]initWithString:lable.text attributes:@{NSKernAttributeName:@(5.0)}];//设置某写字体的颜色//NSForegroundColorAttributeName 设置字体颜色NSRange blueRange = NSMakeRange([[str string] rangeOfString:@"Frank_chun"].location, [[str string] rangeOfString:@"Frank_chun"].length); [str addAttribute:NSForegroundColorAttributeName value:[UIColor redColor] range:blueRange]; NSRange blueRange1 = NSMakeRange([[str string] rangeOfString:@"438637472"].location, [[str string] rangeOfString:@"438637472"].length);[str addAttribute:NSForegroundColorAttributeName value:[UIColor redColor] range:blueRange1];//设置每行之间的间距 //NSParagraphStyleAttributeName 设置段落的样式NSMutableParagraphStyle * par = [[NSMutableParagraphStyle alloc]init];[par setLineSpacing:20];//为某一范围内文字添加某个属性//NSMakeRange表示所要的范围,从0到整个文本的长度[str addAttribute:NSParagraphStyleAttributeName value:par range:NSMakeRange(0, lable.text.length)]; [lable setAttributedText:str];
```

* 30.UITextField默认占位符是居中显示，让其居上显示

```objc
textField.contentVerticalAlignment = UIControlContentVerticalAlignmentTop;
```

* 31.解决同时按两个按钮进两个视图的问题

```objc
[button setExclusiveTouch：YES];
```

* 32.修改textFieldplaceholder字体颜色和大小

```objc
textField.placeholder = @"username is in here!"; [/p][textField setValue:[UIColor redColor] forKeyPath:@"_placeholderLabel.textColor"];  [textField setValue:[UIFont boldSystemFontOfSize:16] forKeyPath:@"_placeholderLabel.font"];
```

* 33.去掉导航栏下边的黑线

```objc
[self.navigationController.navigationBar setBackgroundImage:[[UIImage alloc] init] forBarMetrics:UIBarMetricsDefault];self.navigationController.navigationBar.shadowImage = [[UIImage alloc] init];
```

* 34.通过2D仿射函数实现小的动画效果（变大缩小） - 可用于自定义的PageControl中

```objc
[UIView animateWithDuration:0.3 animations:^{       imageView.transform = CGAffineTransformMakeScale(2, 2);       } completion:^(BOOL finished) {       imageView.transform = CGAffineTransformMakeScale(1.0, 1.0);       }];
```

* 35.修改的PageControl颜色

```objc
_pageControl.currentPageIndicatorTintColor=SFQRedColor;_pageControl.pageIndicatorTintColor=SFQGrayColor;
```

* 36.UIImage的与字符串互转

```objc
//图片转字符串  -(NSString *)UIImageToBase64Str:(UIImage *) image  {      NSData *data = UIImageJPEGRepresentation(image, 1.0f);      NSString *encodedImageStr = [data base64EncodedStringWithOptions:NSDataBase64Encoding64CharacterLineLength];      return encodedImageStr;  } //字符串转图片  -(UIImage *)Base64StrToUIImage:(NSString *)_encodedImageStr  {      NSData *_decodedImageData   = [[NSData alloc] initWithBase64Encoding:_encodedImageStr];      UIImage *_decodedImage      = [UIImage imageWithData:_decodedImageData];      return _decodedImage;  }
```


