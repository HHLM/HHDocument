#iOS小结

* 1、相应点击指定view区域

```objc
- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event
{
    NSSet *allTouches = [event allTouches];    //返回与当前接收者有关的所有的触摸对象
    UITouch *touch = [allTouches anyObject];   //视图中的所有对象
    CGPoint point = [touch locationInView:self.view]; //返回触摸点在视图中的当前坐标
    int x = point.x;
    int y = point.y;
    NSLog(@"touch (x, y) is (%d, %d)", x, y);
}
``` 

* 2、修改UITextField的默认颜色文字大小

```objc
修改默认颜色
    self.textField.placeholder = placeholder;
    [self.textField setValue:[UIColor colorwithHexString:@"cccccc"] forKeyPath:@"_placeholderLabel.textColor"];
    [self.textField setValue:[UIFont boldSystemFontOfSize:14] forKeyPath:@"_placeholderLabel.font"];
```

* 3、去掉字符串的空格

```objc
 NSString *str = @" sdsdijls  ijdlsj sdffsd;kl  fs;dkf;s";
    
 //去掉开头和结尾的空格
 NSString *str1 = [str stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceCharacterSet]];
    
 //替换的方法 。去掉所有空格
 NSString *str2 = [str stringByReplacingOccurrencesOfString:@" " withString:@""];     
 str1 = @"sdsdijls  ijdlsj sdffsd;kl  fs;dkf;s";
 str2 = @"sdsdijlsijdlsjsdffsd;klfs;dkf;s";
```

* 4、修改UITextField文字贴在边框的

```objc

// 默认情况下,当向textField输入文字时,文字会紧贴在textField左边框上.我们可以通过设置textField的leftView,设置一个只有宽度的leftView.这样还不够,因为默认leftView是不显示的.还需要将leftViewMode设置为UITextFieldViewModeAlways.这样就完成了.


    //设置文本框左边的view
    UITextField *textField = [[UITextField alloc]init];
    textField.frame = CGRectMake(10, 30, 300, 30);
    [self.view addSubview:textField];
    textField.leftView = [[UIView alloc]initWithFrame:CGRectMake(0, 0, 8, 0)];
    //设置显示模式为永远显示(默认不显示)
    textField.leftViewMode = UITextFieldViewModeAlways;
```

* 5、取数组中n个元素

```objc
取数组中n个元素

NSArray *array = @[@"品牌",@"颜色",@"上牌时间",@"行驶里程",@"描述"];
NSArray * array1 = [array objectsAtIndexes:[NSIndexSet indexSetWithIndexesInRange:NSMakeRange(1, 3)]];
NSLog(@"array1==%@",array1);
```

* 6、在UITableView上添加tap手势可用的方法:

```objc
  UITapGestureRecognizer *tap = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(tapCounts)];
  tap.cancelsTouchesInView = false;
  [tableView addGestureRecognizer:tap];
```

* 7、UITableviewCell上的控件在点击cell时候背景颜色消失的解决方案：

```objc
lable.layer.backgroundColor=[UIColor redColor].CGColor;
```

* 8、打包上传app时候报错和对应的解决方法

```objc
1、ERROR ITMS-90049: “This bundle is invalid. The bundle identifier contains disallowed characters. [See the section of the Application Programming Guide entitled The Application Bundle.]” 
解决方案：找到第三方库中得info.plist文件添加bundle id ，修改bundle version ，bundle version string 
具体解决步骤：在xcode左下角搜索框搜索info.plist，搜索出所有info.plist文件对这三个key缺少的添加，不对的修改 
2、 
ERROR ITMS-90530: “Invalid MinimumOSVersion. Apps that only support 64-bit devices must specify a deployment target of 8.0 or later. MinimumOSVersion in ‘dqcclient.app’ is ‘7.0’.” 
解决方案：编译时拔掉真机，否者编译会针对真机的系统架构进行编译 
3、 
ERROR ITMS-90535: “Unexpected CFBundleExecutable Key. The bundle at ‘dqcclient.app/TencentOpenApi_IOS_Bundle.bundle’ does not contain a bundle executable. If this bundle intentionally does not contain an executable, consider removing the CFBundleExecutable key from its Info.plist and using a CFBundlePackageType of BNDL. If this bundle is part of a third-party framework, consider contacting the developer of the framework for an update to address this issue.” 
解决方案：在xcode左下角搜索框搜索info.plist，搜索出所有info.plist文件，删除掉第三方info.plist文件中的Executable file 字段 
4、 
ERROR ITMS-90529: “Invalid package. Applications built with sdk 9.0 or later must be packaged as proper IPA files.” 
解决方案：现在上传需是IPA文件 
具体解决步骤：将xcode编译的.app文件放入Payload文件夹内，压缩成.zip文件，修改其后缀为.ipa文件，即可。 
```
* 9、UITableViewCell 上的imageView 用SDWebimage 加载图片时候  出现内存暴涨的现象：

```objc
1.同时加载图片过多；
2.加载的图片过大；
```

* 10、添加延时方法 和取消延时的方法

```objc
[self performSelector:@selector(hidden) withObject:self afterDelay:3];

 [NSObject cancelPreviousPerformRequestsWithTarget:self selector:@selector(hidden) object:nil];//取消对应的方法
    
 [NSObject cancelPreviousPerformRequestsWithTarget:self];//取消全部。
 [[self class] cancelPreviousPerformRequestsWithTarget:self];//取消全部。

```

* 11、判断数组中存在某个元素

```objc
NSArray *array = @[@"品牌",@"颜色",@"上牌时间",@"行驶里程",@"描述"];
//判断数组中存在某个元素
if ([array indexOfObject:@"品牌"]!= NSNotFound) {
        NSLog(@"存在");
    }
```

*  12、让导航条的透明

```objc
[self.navigationController.navigationBar setBackgroundImage:[[UIImage alloc] init] forBarMetrics:UIBarMetricsDefault];
    self.navigationController.navigationBar.shadowImage = [[UIImage alloc] init];
```

* 13、截取图片

```objc
/**
 *  根据imageView的大小设置对应等比例的图片
 *
 *  @param scale imageView的宽高之比
 *
 *  @param image imageView的图片
 *
 *  @return 处理后的图片
 */
- (UIImage *)imageWithScale:(CGFloat)scale image:(UIImage *)image
{
    // scale 是imageView的宽度和高度的比例 image是imageView的图片
    
    CGFloat w  = image.size.width;
    
    CGFloat h = image.size.height;
    
    CGRect rect;
    
    if (w / h > scale) {
    //说明图片的宽度多余  截取图片的宽度是
        
        CGFloat width = image.size.height *scale;
        
        rect =  CGRectMake((w - width)/2, 0,width, h); //这样写是截取图片中间区域
    }
    else
    {
        //说明图片的高度多余 截取图片的高度是
        CGFloat heigh = image.size.width/scale;
        
        rect =  CGRectMake(0, (h - heigh)/2,w, heigh);
    }
    /** < 在原来图片的尺寸的基础上 按照新的尺寸截图 > **/
    image = [UIImage imageWithCGImage:CGImageCreateWithImageInRect([image CGImage], rect)];
    
    return image;
}
```

* 14、保留两位小数点

```objc
- (NSString *)stringWithFloat:(CGFloat)floatS;
{
    float spd = floatS;
    char buf[10];
    sprintf(buf, "%.2f", spd);
    NSString *ss =[NSString stringWithFormat:@"%s",buf];
    return ss;
}
```

* 15、银行卡账号 4位加一空格

```objc
-(NSString *)normalNumToBankNum
{
    NSString *numberStr = @"6221234567890987";
    NSInteger count = numberStr.length / 4;
    NSMutableArray *array = [[NSMutableArray alloc] init];
    for (int n = 0;n < count; n++)
    {
        [array addObject:[numberStr substringWithRange:NSMakeRange(n*4, 4)]];
    }
    [array addObject:[numberStr substringWithRange:NSMakeRange(count*4, (numberStr.length % 4))]];
    
    numberStr = [array componentsJoinedByString:@" "];
    return numberStr;
}
```

* 100、数学公式

```objc
NSLog(@"%i",abs(-3));
    
    NSLog(@"%d",abs(-4));
    
    //floor() 向下取整
    NSLog(@"%f",floorf(-3.1415926));
    NSLog(@"%d",(int)floorf(-3.1415926));
    
    // ceil()向上取整
    NSLog(@"%f",ceilf(3.14));
    NSLog(@"%d",(int)ceilf(3.14));
    
    //round() 四舍五入
    NSLog(@"round()%f",roundf(3.14));
    NSLog(@"round()%d",(int)roundf(3.14));
    NSLog(@"round()%f",roundf(3.74));
    
    //fmax()求两个数的最大值
    NSLog(@"fmax()%f",fmaxf(3.14, 2.71));
    NSLog(@"fmax()%d",(int)fmaxf(3.14, 2.71));
    
    //-------------------  可以嵌套
    NSLog(@"fmax()%f",fmaxf(20.69, fmaxf(3.14, 19.87)));
    
    //fmin()求两个数的最小值
    NSLog(@"fmin%f",fmin(14, 12));
    NSLog(@"fminf()%d",(int)fminf(3.14, 3.141));
    
    //fmod()求两个数整除后的余数
    NSLog(@"fmodf%f",fmodf(5.00, 3.00));
    
    //modf()/modff()/modfl() 浮点数分解为整数和小数
    float a;
    float c;
    float b = 8.22;
    a = modff(b, &c);
    NSLog(@"modf()-整数部分-%f",c);
    NSLog(@"modf()-小数部分-%f",a);

```

打印结果:

```objc
打印结果：
2015-08-13 14:09:55.520 Micro Shop[15342:164204] 3
2015-08-13 14:09:55.564 Micro Shop[15342:164204] 4
2015-08-13 14:09:55.565 Micro Shop[15342:164204] -4.000000
2015-08-13 14:09:55.565 Micro Shop[15342:164204] -4
2015-08-13 14:09:55.565 Micro Shop[15342:164204] 4.000000
2015-08-13 14:09:55.565 Micro Shop[15342:164204] 4
2015-08-13 14:09:55.565 Micro Shop[15342:164204] round()3.000000
2015-08-13 14:09:55.565 Micro Shop[15342:164204] round()3
2015-08-13 14:09:55.565 Micro Shop[15342:164204] round()4.000000
2015-08-13 14:09:55.566 Micro Shop[15342:164204] fmax()3.140000
2015-08-13 14:09:55.566 Micro Shop[15342:164204] fmax()3
2015-08-13 14:09:55.566 Micro Shop[15342:164204] fmax()20.690001
2015-08-13 14:09:55.566 Micro Shop[15342:164204] fmin12.000000
2015-08-13 14:09:55.566 Micro Shop[15342:164204] fminf()3
2015-08-13 14:09:55.566 Micro Shop[15342:164204] fmodf2.000000
2015-08-13 14:09:55.566 Micro Shop[15342:164204] modf()-整数部分-8.000000
2015-08-13 14:09:55.567 Micro Shop[15342:164204] modf()-小数部分-0.220000
```

