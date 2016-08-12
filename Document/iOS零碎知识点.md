#iOS零碎知识点
[TOC]
##1、调用代码使APP进入后台，达到点击Home键的效果。（私有API）

```objc
 [[UIApplication sharedApplication] performSelector:@selector(suspend)];
 suspend的英文意思有：暂停; 悬; 挂; 延缓;
```

##2、带有中文的URL处理。（非UTF-8处理，注意一下）
大概举个例子，类似下面的URL，里面直接含有中文，可能导致播放不了，那么我们要处理一个这个URL，因为他太操蛋了，居然用中文。

```objc
http://static.tripbe.com/videofiles/视频/我的自拍视频.mp4
NSString *path  = (__bridge_transfer NSString *)CFURLCreateStringByReplacingPercentEscapesUsingEncoding(NULL,(__bridge CFStringRef)model.mp4_url,CFSTR(""),CFStringConvertNSStringEncodingToEncoding(NSUTF8StringEncoding));
```
##3、给UIView设置背景图片（UILabel一样适用）

第一种方法：利用的UIView的设置背景颜色方法，用图片做图案颜色，然后传给背景颜色。

```objc
UIColor *bgColor = [UIColor colorWithPatternImage: [UIImage imageNamed:@"bgImg.png"];
UIView *myView = [[UIView alloc] initWithFrame:CGRectMake(0,0,320,480)];
[myView setBackGroundColor:bgColor];
```
第二种方法：

```objc
UIImage *image = [UIImage imageNamed:@"yourPicName@2x.png"];
yourView.layer.contents = (__bridge id)image.CGImage;
//设置显示的图片范围
yourView.layer.contentsCenter = CGRectMake(0.25,0.25,0.5,0.5);//四个值在0-1之间，对应的为x，y，width，height。
```
##4、调整cell分割线的位置

**注意：** 两个方法同时使用

```objc
-(void)viewDidLayoutSubviews {

    if ([self.mytableview respondsToSelector:@selector(setSeparatorInset:)]) {
        [self.mytableview setSeparatorInset:UIEdgeInsetsMake(0, 0, 0, 0)];
    }
    if ([self.mytableview respondsToSelector:@selector(setLayoutMargins:)])  {
        [self.mytableview setLayoutMargins:UIEdgeInsetsMake(0, 0, 0, 0)];
    }
}
#pragma mark - cell分割线
- (void)tableView:(UITableView *)tableView willDisplayCell:(UITableViewCell *)cell forRowAtIndexPath:(NSIndexPath *)indexPath
{
    if ([cell respondsToSelector:@selector(setSeparatorInset:)]){
        [cell setSeparatorInset:UIEdgeInsetsMake(0, 0, 0, 0)];
    }
    if ([cell respondsToSelector:@selector(setLayoutMargins:)]) {
        [cell setLayoutMargins:UIEdgeInsetsMake(0, 0, 0, 0)];
    }
}
```
##5、UISearchController和UISearchBar的Cancle按钮修改title

```objc
- (BOOL)searchBarShouldBeginEditing:(UISearchBar *)searchBar
{
    searchController.searchBar.showsCancelButton = YES;
    UIButton *canceLBtn = [searchController.searchBar valueForKey:@"cancelButton"];
    [canceLBtn setTitle:@"取消" forState:UIControlStateNormal];
    [canceLBtn setTitleColor:[UIColor colorWithRed:14.0/255.0 green:180.0/255.0 blue:0.0/255.0 alpha:1.00] forState:UIControlStateNormal];
    searchBar.showsCancelButton = YES;
    return YES;
}
```
##6、UITableView收起键盘的方法
```objc
方法一：
[self.view endEditing:YES];
方法二：
myTableView.keyboardDismissMode = UIScrollViewKeyboardDismissModeOnDrag;
另外一个枚举为UIScrollViewKeyboardDismissModeInteractive，表示在键盘内部滑动，键盘逐渐下去。
```
##7、UIView的部分圆角问题

```objc
UIView *view = [[UIView alloc] initWithFrame:CGRectMake(120, 10, 80, 80)];
view.backgroundColor = [UIColor redColor];
[self.view addSubview:view];

UIBezierPath *maskPath = [UIBezierPath bezierPathWithRoundedRect:view.bounds byRoundingCorners:UIRectCornerBottomLeft | UIRectCornerBottomRight cornerRadii:CGSizeMake(10, 10)];
CAShapeLayer *maskLayer = [[CAShapeLayer alloc] init];
maskLayer.frame = view.bounds;
maskLayer.path = maskPath.CGPath;
view.layer.mask = maskLayer;
//其中，
byRoundingCorners:UIRectCornerBottomLeft | UIRectCornerBottomRight
//指定了需要成为圆角的角。该参数是UIRectCorner类型的，可选的值有：
* UIRectCornerTopLeft
* UIRectCornerTopRight
* UIRectCornerBottomLeft
* UIRectCornerBottomRight
* UIRectCornerAllCorners
可以组合
```
##8、设置滑动的时候隐藏navigationBar和取消系统的返回手势

```objc
//滑动时候隐藏navigationBar
self.navigationController.hidesBarsOnSwipe = Yes;
//取消系统返回手势
self.navigationController.interactivePopGestureRecognizer.enabled = NO;
```
##9、iOS画虚线 记得先 QuartzCore框架的导入

```objc
#import <QuartzCore/QuartzCore.h>

CGContextRef context =UIGraphicsGetCurrentContext();  
CGContextBeginPath(context);  
CGContextSetLineWidth(context, 2.0);  
CGContextSetStrokeColorWithColor(context, [UIColor whiteColor].CGColor);  
CGFloat lengths[] = {10,10};  
CGContextSetLineDash(context, 0, lengths,2);  
CGContextMoveToPoint(context, 10.0, 20.0);  
CGContextAddLineToPoint(context, 310.0,20.0);  
CGContextStrokePath(context);  
CGContextClosePath(context);  
```
##10、禁止程序运行时自动锁屏 

```objc
[[UIApplication sharedApplication] setIdleTimerDisabled:YES];
```
##11、NSArray 快速求总和 最大值 最小值 和 平均值

```objc
NSArray *array = [NSArray arrayWithObjects:@"2.0", @"2.3", @"3.0", @"4.0", @"10", nil];
CGFloat sum = [[array valueForKeyPath:@"@sum.floatValue"] floatValue];
CGFloat avg = [[array valueForKeyPath:@"@avg.floatValue"] floatValue];
CGFloat max =[[array valueForKeyPath:@"@max.floatValue"] floatValue];
CGFloat min =[[array valueForKeyPath:@"@min.floatValue"] floatValue];
NSLog(@"%f\n%f\n%f\n%f",sum,avg,max,min);
```
##12、强制App退出程序（非闪退，非崩溃）

```objc
- (void)exitApplication 
{
     AppDelegate *app = [UIApplication sharedApplication].delegate;
     UIWindow *window = app.window;
     [UIView animateWithDuration:1.0f animations:^{
             window.alpha = 0;
        } completion:^(BOOL finished) {
             exit(0);
     }];
}
```
##13、把tableview里cell的小对勾的颜色改成别的颜色

```objc
	_myTableView.tintColor = [UIColor redColor];
```
##14、解决同时按两个按钮进两个view的问题

```objc
[button setExclusiveTouch:YES];
```
##15、修改textFieldplaceholder字体颜色和大小
```objc
textField.placeholder = @"请输入用户名";  
[textField setValue:[UIColor redColor] forKeyPath:@"_placeholderLabel.textColor"];  
[textField setValue:[UIFont boldSystemFontOfSize:16] forKeyPath:@"_placeholderLabel.font"];
```
##16、禁止textField和textView的复制粘贴菜单

```objc
-(BOOL)canPerformAction:(SEL)action withSender:(id)sender
{
     if ([UIMenuController sharedMenuController]) {
       [UIMenuController sharedMenuController].menuVisible = NO;
     }
     return NO;
}
```
##17、UIWebView设置字体大小，颜色，字体

```objc
NSString *jsString = [[NSString alloc] initWithFormat:@"document.body.style.fontSize=%f;document.body.style.color=%@",fontSize,fontColor];   
        [webView stringByEvaluatingJavaScriptFromString:jsString];   
```
##18、CocoaPods pod install/pod update更新慢的问题

```
pod install –verbose –no-repo-update 
pod update –verbose –no-repo-update 
如果不加后面的参数，默认会升级CocoaPods的spec仓库，加一个参数可以省略这一步，然后速度就会提升不少。
```
##19、设置UILabel行间距

```objc
 NSMutableAttributedString *attributedString =    
   [[NSMutableAttributedString alloc] initWithString:self.contentLabel.text];
NSMutableParagraphStyle *paragraphStyle =  [[NSMutableParagraphStyle alloc] init];  
[paragraphStyle setLineSpacing:3];
    //调整行间距       
[attributedString addAttribute:NSParagraphStyleAttributeName 
                         value:paragraphStyle 
                         range:NSMakeRange(0,[self.contentLabel.text length])];
self.contentLabel.attributedText = attributedString;
```
##20、删除UIView的所有子view

```objc
//方法一:
[self.subviews makeObjectsPerformSelector:@selector(removeFromSuperview)];
//方法二:
for (id obj in self.subviews)
{
	[obj removeFromSuperview];
}
```

