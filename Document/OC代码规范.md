##OC代码规范

###1.概要
Objective-C 是一门面向对象的动态编程语言，主要用于编写 iOS 和 Mac 应用程序。关于 Objective-C 的编码规范，苹果和谷歌都已经有很好的总结：

* [Apple Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [Google Objective-C Style Guide](https://google.github.io/styleguide/objcguide.xml)



###2.代码格式

####缩进符

* [不要在工程里使用 Tab 键，使用空格来进行缩进。在 Xcode > Preferences > Text Editing 将 Tab 和自动缩进都设置为 4 个空格。（Google 的标准是使用两个空格来缩进，但这里还是推荐使用 Xcode 默认的设置。）

####每行的长度

* [建议] 应尽量控制每行代码的长度在120个字符以内。同样的，在 Xcode > Preferences > Text Editing > Page guide at column: 中将最大行长设置为 120 ，过长的一行代码将会导致可读性问题。

####函数的书写

一个典型的 Objective-C 函数应该是这样的：

```objc
- (void)writeVideoFrameWithData:(NSData *)frameData timeStamp:(int)timeStamp {
...
}
```
在 - 和 (void) 之间应该有一个空格，第一个大括号 { 的位置在函数所在行的末尾，同样应该有一个空格。（我司的 C 语言规范要求是第一个大括号单独占一行，但考虑到 OC 较长的函数名和苹果 SDK 代码的风格，还是将大括号放在行末。）

如果一个函数有特别多的参数或者名称很长，应该将其按照 : 来对齐分行显示：

```objc
-(id)initWithModel:(IPCModle)model
       ConnectType:(IPCConnectType)connectType
        Resolution:(IPCResolution)resolution
          AuthName:(NSString *)authName
          Password:(NSString *)password
               MAC:(NSString *)mac
              AzIp:(NSString *)az_ip
             AzDns:(NSString *)az_dns
             Token:(NSString *)token
             Email:(NSString *)email
          Delegate:(id)delegate;
```
在分行时，如果第一段名称过短，后续名称可以以 Tab 的长度（ 4 个空格）为单位进行缩进：

```objc
- (void)short:(GTMFoo *)theFoo
		      longKeyword:(NSRect)theRect
		evenLongerKeyword:(float)theInterval
		            error:(NSError **)theError {
...
}
```

####函数调用

* [建议] 方法调用的格式须要与定义时的格式一致。当有多种格式化的样式可供选择的时候,按照惯例,采用在给定的源文件中使用过的那个方式。

```objc
// 写在一行
[myObject doFooWith:arg1 name:arg2 error:arg3];

// 分行写，按照 ':' 对齐
[myObject doFooWith:arg1
               name:arg2
              error:arg3];
              
// 第一段名称过短的话后续可以进行缩进
[myObj short:arg1
        longKeyword:arg2
  evenLongerKeyword:arg3
              error:arg4];
 
```


####逗号分隔项

* [强制] 用逗号分隔多项时,每个逗号后使用1个空格进行分隔。

####左大括号位置

* [建议] 左大括号 { 不单独占据一行,放置在上一行的末尾,可以在 { 前增加一个空格

####声明与定义
*  [强制] ‐,+ 与返回类型之间必须有一个空格,在参数列表中,除了参数之间不要有任何间距。 示例

```objc
- (void)doSomethingWithString:(NSString *)theString { ...}
```

* [强制] * 号前面必须要加空格,增加可读性。
* [建议] 如果有参数太多无法放在同一行内, 最好每个参数各自一行。如果采用多行,每个参数建议按照 : 进行对齐。示例

```objc
- (void)doSomethingWith:(GTMFoo *)theFo 
				   rect:(NSRect)theRect 
			   interval:(float)theInterval {... 
}
```

####@public、@protected与@private

* [强制] 访问修饰符 @public, @private,@protected必须缩进1个空格。

```objc
@interface MyClass : NSObject {
@public
...
@private
...
}
@end
```


####协议（ Protocols ）
* [强制] 在类型标识符和封装在尖括号中的 Protocols 名称之间要有空格。

在书写协议的时候注意用 <> 括起来的协议和类型名之间是没有空格的，比如 IPCConnectHandler(), 这个规则适用所有书写协议的地方，包括函数声明、类声明、实例变量等等：
解释这些适用于类的声明、实例变量和方法的声明示例

```objc
@interface MyProtocoledClass : NSObject <NSWindowDelegate> { 
@privateid <MyFancyDelegate> delegate_; 
}- (void)setDelegate:(id <MyFancyDelegate>)aDelegate; 
@end
```
####闭包(Blocks)
* [强制] 块内的代码应缩进4个空格。* [建议] 因为具体block长度的不同,可以有以下几种风格:* 较短的 block 可以写在一行内。
* 如果分行显示的话， block 的右括号 } 应该和调用 block 那行代码的第一个非空字符对齐。
* 如果 block 过于庞大，应该单独声明成一个变量来使用。
* ^ 和 ( 之间， ^ 和 { 之间都没有空格，参数列表的右括号 ) 和 { 之间有一个空格。
例子

```objc
// 较短的 block 写在一行内
[operation setCompletionBlock:^{ [self onOperationDone]; }];
// 分行书写的 block ，内部使用 4 空格缩进
[operation setCompletionBlock:^{
[self.delegate newDataAvailable];
}];
// 使用 C 语言 API 调用的 block 遵循同样的书写规则
dispatch_async(_fileIOQueue, ^{
NSString* path = [self sessionFilePath];
if (path) {
// ...
}
});
// 较长的 block 关键字可以缩进后在新行书写，注意 block 的右括号 '}' 和调用 block 那行代码的第一个非空字符对齐
[[SessionService sharedService]
loadWindowWithCompletionBlock:^(SessionWindow *window) {
if (window) {
[self windowDidLoad:window];
} else {
[self errorLoadingWindow];
}
}];
// 较长的 block 参数列表同样可以缩进后在新行书写
[[SessionService sharedService]
loadWindowWithCompletionBlock:
^(SessionWindow *window) {
if (window) {
[self windowDidLoad:window];
} else {
[self errorLoadingWindow];
}
}];
// 庞大的 block 应该单独定义成变量使用
void (^largeBlock)(void) = ^{
// ...
};
[_operationQueue addOperationWithBlock:largeBlock];
// 在一个调用中使用多个 block ，注意到他们不是像函数那样通过 ':' 对齐的，而是同时进行了 4 个空格的缩进
[myObject doSomethingWith:arg1
firstBlock:^(Foo *a) {
// ...
}
secondBlock:^(Bar *b) {
// ...
}];
```

####Container Literals
* [强制] 使用容器(数组和字典)常量,如果其内容被分为多行,应该缩进4个空格。
* [建议] 如果内容单行就能放下,在左大括号之后和右大括号之前各添加一个空格。
例子

```objc
 // 正确，在语法糖的 "[]" 或者 "{}" 两端留有空格
NSArray *array = @[ [foo description], @"Another String", [bar description] ];
NSDictionary *dict = @{ NSForegroundColorAttributeName : [NSColor redColor] };
// 不正确，不留有空格降低了可读性
NSArray* array = @[[foo description], [bar description]];
NSDictionary* dict = @{NSForegroundColorAttributeName: [NSColor redColor]};
```
* [建议] 如果内容跨越多行,将左括号和声明放在同一行,之后换行的内容缩进4个空格,并将右括号单独放在新的一行里 和声明行对齐。

```onjc
    NSArray *array = @[ @"This",                        @"is",
                        @"an",
                        @"array"];    NSDictionary *dictionary = @{                                 NSFontAttributeName : [NSFont fontWithName:@"Helvetica-Bold" size:12],                                 NSForegroundColorAttributeName : fontColor };
```
* [强制] 对于字典常量,在 : 之前不加空格,之后至少一个空格(或者空格的数量能够满足对齐的要求)

###3.命名规范
####基本原则

__清晰__

- 文件名字以ZX（掌讯）开头

- 命名应该尽可能的清晰和简洁，但在 Objective-C 中，清晰比简洁更重要。由于 Xcode 强大的自动补全功能，我们不必担心名称过长的问题。  

```objc
// 清晰
insertObject:atIndex:
// 不清晰， insert 的对象类型和 at 的位置属性没有说明
insert:at:
// 清晰
removeObjectAtIndex:
// 不清晰， remove 的对象类型没有说明，参数的作用没有说明
remove:
```

- 不要使用单词的简写，拼写出完整的单词：

```objc
// 清晰
destinationSelection
setBackgroundColor:
// 不清晰，不要使用简写
destSel
setBkgdColor:
```
然而，有部分单词简写在 Objective-C 编码过程中是非常常用的，以至于成为了一种规范，这些简写可以在代码中直接使用，下面列举了部分：

```objc
alloc == Allocate max == Maximum
alt == Alternate min == Minimum
app == Application msg == Message
calc == Calculate nib == Interface Builder archive
dealloc == Deallocate pboard == Pasteboard
func == Function rect == Rectangle
horiz == Horizontal Rep == Representation (used in class name such as NSBitmapImageRep).
info == Information temp == Temporary
init == Initialize vert == Vertical
int == Integer
```
- 命名方法或者函数时要避免歧义

```objc
// 有歧义，是返回 sendPort 还是 send 一个 Port ？
sendPort
// 有歧义，是返回一个名字属性的值还是 display 一个 name 的动作？
displayName
```

* 一致性

整个工程的命名风格要保持一致性，最好和苹果 SDK 的代码保持统一。不同类中完成相似功能的方法应该叫一样的名字，比如我们总是用 count 来返回集合的个数，不能在 A 类中使用 count 而在 B 类中使用 getNumber 。

####使用前缀

如果代码需要打包成 Framework 给别的工程使用，或者工程项目非常庞大，需要拆分成不同的模块，使用命名前缀是非常有用的。

	* 前缀由大写的字母缩写组成，比如 Cocoa 中 NS 前缀代表 Founation 框架中的类， IB 则代表 Interface Builder 框架。

	* 可以在为类、协议、函数、常量以及 typedef 宏命名的时候使用前缀，但注意不要为成员变量或者方法使用前缀，因为他们本身就包含在类的命名空间内。

	* 命名前缀的时候不要和苹果 SDK 框架冲突。

####命名类和协议（ Class&Protocol ）
	
 	类名以大写字母开头，应该包含一个名词来表示它代表的对象类型，同时可以加上必要的前缀，比如 NSString,NSDate,NSScanner,NSApplication 等等。

	而协议名称应该清晰地表示它所执行的行为，而且要和类名区别开来，所以通常使用 ing 词尾来命名一个协议，比如 NSCopying,NSLocking 。

	有些协议本身包含了很多不相关的功能，主要用来为某一特定类服务，这时候可以直接用类名来命名这个协议，比如 NSObject 协议，它包含了 id 对象在生存周期内的一系列方法。

####命名头文件（ Headers ）

源码的头文件名应该清晰地暗示它的功能和包含的内容：

	* 如果头文件内只定义了单个类或者协议，直接用类名或者协议名来命名头文件，比如 NSLocale.h 定义了 NSLocale 类。
	
	* 如果头文件内定义了一系列的类、协议、类别，使用其中最主要的类名来命名头文件，比如 NSString.h 定义了 NSString 和 NSMutableString 。
	 
	* 每一个 Framework 都应该有一个和框架同名的头文件，包含了框架中所有公共类头文件的引用，比如 Foundation.h
	 
	* Framework 中有时候会实现在别的框架中类的类别扩展，这样的文件通常使用被扩展的框架名 +Additions 的方式来命名，比如 NSBundleAdditions.h 。


####命名方法（ Methods ）

Objective-C 的方法名通常都比较长，这是为了让程序有更好地可读性，按苹果的说法 “ 好的方法名应当可以以一个句子的形式朗读出来 ” 。

方法一般以小写字母打头，每一个后续的单词首字母大写，方法名中不应该有标点符号（包括下划线），有两个例外：


* 可以用一些通用的大写字母缩写打头方法，比如 PDF,TIFF 等。
	
* 可以用带下划线的前缀来命名私有方法或者类别中的方法。

如果方法表示让对象执行一个动作，使用动词打头来命名，注意不要使用 do ， does 这种多余的关键字，动词本身的暗示就足够了：

```OBJC
// 动词打头的方法表示让对象执行一个动作
- (void)invokeWithTarget:(id)target;
- (void)selectTabViewItem:(NSTabViewItem *)tabViewItem;
```
如果方法是为了获取对象的一个属性值，直接用属性名称来命名这个方法，注意不要添加 get 或者其他的动词前缀

```objc
// 正确，使用属性名来命名方法
- (NSSize)cellSize;
// 错误，添加了多余的动词前缀
- (NSSize)calcCellSize;
- (NSSize)getCellSize;
```
对于有多个参数的方法，务必在每一个参数前都添加关键词，关键词应当清晰说明参数的作用：

```objc
// 正确，保证每个参数都有关键词修饰
- (void)sendAction:(SEL)aSelector toObject:(id)anObject forAllCells:(BOOL)flag;
// 错误，遗漏关键词
- (void)sendAction:(SEL)aSelector :(id)anObject :(BOOL)flag;
// 正确
- (id)viewWithTag:(NSInteger)aTag;
// 错误，关键词的作用不清晰
- (id)taggedView:(int)aTag;
```
不要用 and 来连接两个参数，通常 and 用来表示方法执行了两个相对独立的操作（从设计上来说，这时候应该拆分成两个独立的方法）：

```objc
// 错误，不要使用 "and" 来连接参数
- (int)runModalForDirectory:(NSString *)path andFile:(NSString *)name andTypes:(NSArray *)fileTypes;
// 正确，使用 "and" 来表示两个相对独立的操作
- (BOOL)openFile:(NSString *)fullPath withApplication:(NSString *)appName andDeactivate:(BOOL)flag;
```
方法的参数命名也有一些需要注意的地方 :

* 和方法名类似，参数的第一个字母小写，后面的每一个单词首字母大写
* 不要再方法名中使用类似 pointer,ptr 这样的字眼去表示指针，参数本身的类型足以说明
* 不要使用只有一两个字母的参数名
* 不要使用简写，拼出完整的单词

下面列举了一些常用参数名：

```objc
...action:(SEL)aSelector
...alignment:(int)mode
...atIndex:(int)index
...content:(NSRect)aRect
...doubleValue:(double)aDouble
...floatValue:(float)aFloat
...font:(NSFont *)fontObj
...frame:(NSRect)frameRect
...intValue:(int)anInt
...keyEquivalent:(NSString *)charCode
...length:(int)numBytes
...point:(NSPoint)aPoint
...stringValue:(NSString *)aString
...tag:(int)anInt
...target:(id)anObject
...title:(NSString *)aString
```

####存取方法（ Accessor Methods ）

存取方法是指用来获取和设置类属性值的方法，属性的不同类型，对应着不同的存取方法规范：

```objc
// 属性是一个名词时的存取方法范式
- (type)noun;
- (void)setNoun:(type)aNoun;
// 栗子
- (NSString *)title;
- (void)setTitle:(NSString *)aTitle;
// 属性是一个形容词时存取方法的范式
- (NSString *)title;
- (void)setTitle:(NSString *)aTitle;
// 栗子
- (BOOL)isAdjective;
- (void)setAdjective:(BOOL)flag;
// 属性是一个动词时存取方法的范式
- (BOOL)verbObject;
- (void)setVerbObject:(BOOL)flag;
// 栗子
- (BOOL)showsAlpha;
- (void)setShowsAlpha:(BOOL)flag;
```
命名存取方法时不要将动词转化为被动形式来使用：

```objc
// 正确
- (void)setAcceptsGlyphInfo:(BOOL)flag;
- (BOOL)acceptsGlyphInfo;
// 错误，不要使用动词的被动形式
- (void)setGlyphInfoAccepted:(BOOL)flag;
- (BOOL)glyphInfoAccepted;
```

可以使用 can,should,will 等词来协助表达存取方法的意思，但不要使用 do, 和 does ：

```objc
// 正确
- (void)setCanHide:(BOOL)flag;
- (BOOL)canHide;
- (void)setShouldCloseDocument:(BOOL)flag;
- (BOOL)shouldCloseDocument;
// 错误，不要使用 "do" 或者 "does"
- (void)setDoesAcceptGlyphInfo:(BOOL)flag;
- (BOOL)doesAcceptGlyphInfo;
```
为什么 Objective-C 中不适用 get 前缀来表示属性获取方法？因为 get 在 Objective-C 中通常只用来表示从函数指针返回值的函数：

```objc
// 三个参数都是作为函数的返回值来使用的，这样的函数名可以使用 "get" 前缀
- (void)getLineDash:(float *)pattern count:(int *)count phase:(float *)phase;
```
####命名委托（ Delegate ）

当特定的事件发生时，对象会触发它注册的委托方法。委托是 Objective-C 中常用的传递消息的方式。委托有它固定的命名范式。

一个委托方法的第一个参数是触发它的对象，第一个关键词是触发对象的类名，除非委托方法只有一个名为 sender 的参数：

```objc
// 第一个关键词为触发委托的类名
- (BOOL)tableView:(NSTableView *)tableView shouldSelectRow:(int)row;
- (BOOL)application:(NSApplication *)sender openFile:(NSString *)filename;
// 当只有一个 "sender" 参数时可以省略类名
- (BOOL)applicationOpenUntitledFile:(NSApplication *)sender;
根据委托方法触发的时机和目的，使用 should,will,did 等关键词
- (void)browserDidScroll:(NSBrowser *)sender;
- (NSUndoManager *)windowWillReturnUndoManager:(NSWindow *)window; 、
- (BOOL)windowShouldClose:(id)sender;
```
####集合操作类方法（ Collection Methods ）

有些对象管理着一系列其它对象或者元素的集合，需要使用类似 “ 增删查改 ” 的方法来对集合进行操作，这些方法的命名范式一般为：

```objc
// 集合操作范式
- (void)addElement:(elementType)anObj;
- (void)removeElement:(elementType)anObj;
- (NSArray *)elements;
// 栗子
- (void)addLayoutManager:(NSLayoutManager *)obj;
- (void)removeLayoutManager:(NSLayoutManager *)obj;
- (NSArray *)layoutManagers;
```

注意，如果返回的集合是无序的，使用 NSSet 来代替 NSArray 。如果需要将元素插入到特定的位置，使用类似于这样的命名：

```objc
- (void)insertLayoutManager:(NSLayoutManager *)obj atIndex:(int)index;
- (void)removeLayoutManagerAtIndex:(int)index;
```
如果管理的集合元素中有指向管理对象的指针，要设置成 weak 类型以防止引用循环。

下面是 SDK 中 NSWindow 类的集合操作方法：

```objc
- (void)addChildWindow:(NSWindow *)childWin ordered:(NSWindowOrderingMode)place;
- (void)removeChildWindow:(NSWindow *)childWin;
- (NSArray *)childWindows;
- (NSWindow *)parentWindow;
- (void)setParentWindow:(NSWindow *)window;
```

####命名函数（ Functions ）

在很多场合仍然需要用到函数，比如说如果一个对象是一个单例，那么应该使用函数来代替类方法执行相关操作。

函数的命名和方法有一些不同，主要是：

* 函数名称一般带有缩写前缀，表示方法所在的框架。

* 前缀后的单词以 “ 驼峰 ” 表示法显示，第一个单词首字母大写。

函数名的第一个单词通常是一个动词，表示方法执行的操作：

```objc
NSHighlightRect
NSDeallocateObject
```

如果函数返回其参数的某个属性，省略动词：

```objc
unsigned int NSEventMaskFromType(NSEventType type)
float NSHeight(NSRect aRect)
```

如果函数通过指针参数来返回值，需要在函数名中使用 Get ：

```objc
unsigned int NSEventMaskFromType(NSEventType type)
float NSHeight(NSRect aRect)
```
函数的返回类型是 BOOL 时的命名：

```objc
BOOL NSDecimalIsNotANumber(const NSDecimal *decimal)
```

####命名属性和实例变量（ Properties&Instance Variables ）

属性和对象的存取方法相关联，属性的第一个字母小写，后续单词首字母大写，不必添加前缀。属性按功能命名成名词或者动词：

```objc
// 名词属性
@property (strong) NSString *title;
// 动词属性
@property (assign) BOOL showsAlpha;
```

属性也可以命名成形容词，这时候通常会指定一个带有 is 前缀的 get 方法来提高可读性：

```objc
@property (assign, getter=isEditable) BOOL editable;
```
命名实例变量，在变量名前加上 _ 前缀（有些有历史的代码会将 _ 放在后面 ），其它和命名属性一样：

```objc
@implementation MyClass {
BOOL _showsTitle;
}
```

一般来说，类需要对使用者隐藏数据存储的细节，所以不要将实例方法定义成公共可访问的接口，可以使用 @private ， @protected 前缀。

按苹果的说法，不建议在除了 init 和 dealloc 方法以外的地方直接访问实例变量，但很多人认为直接访问会让代码更加清晰可读，只在需要计算或者执行操作的时候才使用存取方法访问，我就是这种习惯，所以这里不作要求。

####命名常量（ Constants ）


使用 const 定义浮点型或者单个的整数型常量，如果要定义一组相关的整数常量，应该优先使用枚举。常量的命名规范和函数相同：

```objc
const float NSLightGray;
```
不要使用 #define 宏来定义常量，如果是整型常量，尽量使用枚举，浮点型常量，使用 const 定义。 #define 通常用来给编译器决定是否编译某块代码，比如常用的：

```objc
#ifdef DEBUG
```
注意到一般由编译器定义的宏会在前后都有一个 __ ，比如 `__MACH__` 。


####命名通知（ Notifications ）

通知常用于在模块间传递消息，所以通知要尽可能地表示出发生的事件，通知的命名范式是：

```objc
[ 触发通知的类名 ] + [Did | Will] + [ 动作 ] + Notification
```
栗子：

```objc
NSApplicationDidBecomeActiveNotification
NSWindowDidMiniaturizeNotification
NSTextViewDidChangeSelectionNotification
NSColorPanelColorDidChangeNotification
```
###4注释

####注释文件
每一个文件都必须写文件注释，文件注释通常包含

	文件所在模块
	作者信息
	历史版本信息
	版权信息
	文件包含的内容，作用

####注释方法
有很多可以自动生成注释格式的插件，推荐使用 [VVDocumenter](https://github.com/onevcat/VVDocumenter-Xcode) 插件：

![](http://cc.cocimg.com/api/uploads/20150507/1430987627931187.gif)


_定义在头文件里的接口方法、属性必须要有注释！_

####注释属性

 可以做成代码块 调用方便

```objc
/**
	<#Description#>
*/
/**
	标题名称
 */
@property (nonatomic, copy) NSString *title;
```
或者

```objc
 //!< <#Description#> 
 @property (nonatomic, copy) NSString *title;//!< 标题名称
```

###5编码风格

每个人都有自己的编码风格，这里总结了一些比较好的 Cocoa 编程风格和注意点。

####不要使用 new 方法

尽管很多时候能用 new 代替 alloc init 方法，但这可能会导致调试内存时出现不可预料的问题。 Cocoa 的规范就是使用 alloc init 方法，使用 new 会让一些读者困惑。

####Public API 要尽量简洁

共有接口要设计的简洁，满足核心的功能需求就可以了。不要设计很少会被用到，但是参数极其复杂的 API 。如果要定义复杂的方法，使用类别或者类扩展。

#### #import 和 #include

_#import 是 Cocoa 中常用的引用头文件的方式，它能自动防止重复引用文件，什么时候使用 #import ，什么时候使用 #include 呢？_

	当引用的是一个 Objective-C 或者 Objective-C++ 的头文件时，使用 #import
	当引用的是一个 C 或者 C++ 的头文件时，使用 #include ，这时必须要保证被引用的文件提供了保护域（ #define guard ）。
栗子：

```objc
#import
#include
#import "GTMFoo.h"
#include "base/basictypes.h"
```
为什么不全部使用 #import 呢？主要是为了保证代码在不同平台间共享时不出现问题。

####BOOL 的使用

BOOL 在 Objective-C 中被定义为 signed char 类型，这意味着一个 BOOL 类型的变量不仅仅可以表示 YES(1) 和 NO(0) 两个值，所以永远不要将 BOOL 类型变量直接和 YES 比较：

```objc
// 错误，无法确定 |great| 的值是否是 YES(1) ，不要将 BOOL 值直接与 YES 比较
BOOL great = [foo isGreat];
if (great == YES)
// ...be great!
// 正确
BOOL great = [foo isGreat];
if (great)
// ...be great!
```
同样的，也不要将其它类型的值作为 BOOL 来返回，这种情况下， BOOL 变量只会取值的最后一个字节来赋值，这样很可能会取到 0 （ NO ）。但是，一些逻辑操作符比如 &&,||,! 的返回是可以直接赋给 BOOL 的：

```objc
// 错误，不要将其它类型转化为 BOOL 返回
- (BOOL)isBold {
return [self fontTraits] & NSFontBoldTrait;
}
- (BOOL)isValid {
return [self stringValue];
}
// 正确
- (BOOL)isBold {
return ([self fontTraits] & NSFontBoldTrait) ? YES : NO;
}
// 正确，逻辑操作符可以直接转化为 BOOL
- (BOOL)isValid {
return [self stringValue] != nil;
}
- (BOOL)isEnabled {
return [self isValid] && [self isBold];
}
```
另外 BOOL 类型可以和 _Bool,bool 相互转化，但是不能和 Boolean 转化。


####使用 ARC

除非想要兼容一些古董级的机器和操作系统，我们没有理由放弃使用 ARC 。在最新版的 Xcode(6.2) 中， ARC 是自动打开的，所以直接使用就好了。

####在 init 和 dealloc 中不要用存取方法访问实例变量

当 initdealloc 方法被执行时，类的运行时环境不是处于正常状态的，使用存取方法访问变量可能会导致不可预料的结果，因此应当在这两个方法内直接访问实例变量。

```objc
// 正确，直接访问实例变量
- (instancetype)init {
self = [super init];
if (self) {
_bar = [[NSMutableString alloc] init];
}
return self;
}
- (void)dealloc {
[_bar release];
[super dealloc];
}
// 错误，不要通过存取方法访问
- (instancetype)init {
self = [super init];
if (self) {
self.bar = [NSMutableString string];
}
return self;
}
- (void)dealloc {
self.bar = nil;
[super dealloc];
}
```

####使用 NSNumber 的语法糖

使用带有 @ 符号的语法糖来生成 NSNumber 对象能使代码更简洁：

```objc
NSNumber *fortyTwo = @42;
NSNumber *piOverTwo = @(M_PI / 2);
enum {
kMyEnum = 2;
};
NSNumber *myEnum = @(kMyEnum);
```
####nil 检查

因为在 Objective-C 中向 nil 对象发送命令是不会抛出异常或者导致崩溃的，只是完全的 “ 什么都不干 ” ，所以，只在程序中使用 nil 来做逻辑上的检查。

另外，不要使用诸如 nil == Object 或者 Object == nil 的形式来判断。

```objc
// 正确，直接判断
if (!objc) {
...
}
// 错误，不要使用 nil == Object 的形式
if (nil == objc) {
...
}
```

####Delegate 要使用弱引用

一个类的 Delegate 对象通常还引用着类本身，这样很容易造成引用循环的问题，所以类的 Delegate 属性要设置为弱引用。

```objc
/** delegate */
@property (nonatomic, weak) iddelegate;
```
[TOC]


