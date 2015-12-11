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
3.assign：set方法的实现是直接赋值，用于基本数据类型
4.retain：set方法的实现是release旧值，retain新值，用于OC对象类型
5.copy：set方法的实现是release旧值，copy新值，用于NSString、block等类型
6.nonatomic：非原子性，set方法的实现不加锁（比atomic性能高），绝大多数的属性都是nonatomic
```
3.写一个setter方法用于完成@property （nonatomic,retain）NSString *name,写一个setter方法用于完成@property（nonatomic，copy）NSString *name.
```
@property (nonatomic, retain) NSString *name;
- (void)setName:(NSString *)name{
	if (_name != name) {
		[_name release];
		_name = [name retain];
  }
}
@property(nonatomic, copy) NSString *name;
- (void)setName:(NSString *)name{
  if(_name != name){
    [_name release];
    _name = [name copy];
  }
}
```
4.对于语句NSString *obj = [[NSData alloc] init]; ，编译时和运行时obj分别是什么类型？
```

```
