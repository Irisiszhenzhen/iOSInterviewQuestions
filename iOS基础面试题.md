# iOS基础面试题
1.#import 跟#include、@class有什么区别？＃import<> 跟 #import”"又什么区别？ 

```
#import 和#include都是在当前文件引入某个文件的内容，#import能防止同一个文件被引用多次
@class 仅仅声明一个类名，并不包含类的完整声明，@class还能解决循环包换的问题
#import <> 用来包含系统自带的文件，#import “”用来包含自定义的文件
```
2.属性readwrite，readonly，assign，retain，copy，nonatomic 各是什么作用，在那种情况下用？
```
1.readwrite：同时生成get方法和set方法的声明和实现  
2.readonly：只生成get方法的声明和实现
3.
```