#runtime学习总结

>runtime的理解

* runtime 就是在运行的过程中转成C语言的代码 例如：` [self changeTextFont];` 会被转化成``` objc_msgSend(self,@select(changeTextFont)); ```
* 在runtime中一个对象就是用结构体来表示的


```objc
/// An opaque type that represents a method in a class definition. 描述类中的一个方法
typedef struct objc_method *Method;

/// An opaque type that represents an instance variable. 实例的变量
typedef struct objc_ivar *Ivar;

/// An opaque type that represents a category. 扩展
typedef struct objc_category *Category;

/// An opaque type that represents an Objective-C declared property. 描述成员属性
typedef struct objc_property *objc_property_t;

```
* runtime中的表示

```objc
struct objc_class {
    Class isa ;

#if !__OBJC2__
    Class super_class ;	//指向父类                                       
    const char *name ;  //类名
    long version ;                                            
    long info ;                                               
    long instance_size;                                       
    struct objc_ivar_list *ivars;           //成员变量列表 
    struct objc_method_list **methodLists;  //方法列表              
    struct objc_cache *cache ;              //缓存                           
    struct objc_protocol_list *protocols ;  //协议列表                  
#endif

} OBJC2_UNAVAILABLE;
```
* 获取类的属性列表

```objc
unsigned int count;
    objc_property_t *propertyList = class_copyPropertyList([self class], &count);
    for (unsigned int i = 0; i < count; i++) {
        const char *propertyName = property_getName(propertyList[i]);
        NSLog(@"proprety--->%@",[NSString stringWithUTF8String:propertyName]);
    }
```

* 获取类的成员变量

```objc
unsigned int count;
//获取成员变量列表
Ivar *ivarList = class_copyIvarList([self class], &count);
for (unsigned int i; i<count; i++) {
    Ivar ivar = ivarList[i];
    const char *ivarname = ivar_getName(ivar);                
    NSLog(@"ivar----="">%@", [NSString stringWithUTF8String:ivarname]);
}
```

* 获取类的所有方法

```objc
//获取self的所有的方法
- (void)getMethods
{
    unsigned int count;
    Method *methodlist = class_copyMethodList([self class], &count);
    for (int i = 0; i < count; i ++) {
        Method method = methodlist[i];
        NSLog(@"method---> %@",NSStringFromSelector(method_getName(method)));
    }
}
```
* 获取类遵循的协议

```objc
//获取所有self类的协议
- (void)getProtocals
{
    unsigned int count;
    __unsafe_unretained Protocol **protocolLsit = class_copyProtocolList([self class], &count);
    for (int i = 0; i < count; i++) {
        Protocol *protocol = protocolLsit[i];
        const char  *protoalName = protocol_getName(protocol);
        NSLog(@"protocol---> %@",[NSString stringWithUTF8String:protoalName]);
    }
}
```
* 可以给category添加属性

```objc
//用他的地址作为关联对象的key
static char objcKey; 
//动态添加属性
- (void)setObjcName:(NSString *)objcName
{
    objc_setAssociatedObject(self, &objcKey, objcName, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
   
}
- (NSString *)objcName
{
    return  objc_getAssociatedObject(self, &objcKey);;
}
```
* 打印结果

```objc
2016-03-31 18:19:34.350 RumtimeModel[9164:325075] ivar--->_girl0
2016-03-31 18:19:34.350 RumtimeModel[9164:325075] ivar--->_girl1
2016-03-31 18:19:34.351 RumtimeModel[9164:325075] ivar--->_girl2
2016-03-31 18:19:34.351 RumtimeModel[9164:325075] ivar--->_girl3
2016-03-31 18:19:34.352 RumtimeModel[9164:325075] proprety--->girl0
2016-03-31 18:19:34.353 RumtimeModel[9164:325075] proprety--->girl1
2016-03-31 18:19:34.353 RumtimeModel[9164:325075] proprety--->girl2
2016-03-31 18:19:34.353 RumtimeModel[9164:325075] proprety--->girl3
2016-03-31 18:19:34.388 RumtimeModel[9164:325075] method---> setDicData:
2016-03-31 18:19:34.389 RumtimeModel[9164:325075] method---> dicData
2016-03-31 18:19:34.389 RumtimeModel[9164:325075] method---> getMethods
2016-03-31 18:19:34.389 RumtimeModel[9164:325075] method---> getProtocals
2016-03-31 18:19:34.389 RumtimeModel[9164:325075] method---> categotyString
2016-03-31 18:19:34.390 RumtimeModel[9164:325075] method---> .cxx_destruct
2016-03-31 18:19:34.390 RumtimeModel[9164:325075] method---> tableView:numberOfRowsInSection:
2016-03-31 18:19:34.390 RumtimeModel[9164:325075] method---> tableView:cellForRowAtIndexPath:
2016-03-31 18:19:34.390 RumtimeModel[9164:325075] method---> numberOfSectionsInTableView:
2016-03-31 18:19:34.391 RumtimeModel[9164:325075] method---> didReceiveMemoryWarning
2016-03-31 18:19:34.391 RumtimeModel[9164:325075] method---> viewDidLoad
2016-03-31 18:19:34.391 RumtimeModel[9164:325075] protocol---> UITableViewDataSource
2016-03-31 18:19:34.391 RumtimeModel[9164:325075] protocol---> UITableViewDelegate
2016-03-31 18:19:34.392 RumtimeModel[9164:325075] category----> HHLM
```


