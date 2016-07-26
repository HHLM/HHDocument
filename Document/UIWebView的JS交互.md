#UIWebView的JS交互

##交互的方式
>系统自带的交互
>调用JavaScript的方法


<!-- more -->



###使用系统的方法
```
1.设置webView的代理方法

2.在下面方法里面 获取到点击对应的URL 然后执行相关的事件

- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType
{
    NSString *requestURL = [[request URL] absoluteString] ;
 	//requestURL 就是获取到的需要的数据 然后执行对应事件就OK了
    return YES;
}
```
###使用javaScript的方法

1.同样设置webView的代理方法

2.导入头文件 `#import<JavaScriptCore/JavaScriptCore.h>` 

3.声明一个JSContext 类型的属性 context

4.在加载完成的代理方法里面 写js的交互的方法

```
	
```



