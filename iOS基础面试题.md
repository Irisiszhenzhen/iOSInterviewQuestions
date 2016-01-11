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
4.set方法的实现是release旧值，retain新值，用于OC对象类型
5.copy：set方法的实现是release旧值，copy新值，用于NSString、block等类型
6.nonatomic：非原子性，set方法的实现不加锁（比atomic性能高）
```
3.写一个setter方法用于完成@property （nonatomic,retain）NSString *name,写一个setter方法用于完成@property（nonatomic，copy）NSString *name.

```
@property (nonatomic, retain) NSString *name;
- (void)setName:(NSString *)name
{
	if (_name != name) {
		[_name release];
		_name = [name retain];
}
}

@property(nonatomic, copy) NSString *name;
- (void)setName:(NSString *)name
{
	if (_name != name) {
		[_name release];
		_name = [name copy];
}
}
```

4.对于语句NSString *obj = [[NSData alloc] init]; ，编译时和运行时obj分别是什么类型？

```
此题主要考察对运行时机制的理解，编译时是NSString类型 ，运行时是NSData类型.
NSString *obj 对obj的类型没有任何的作用，只是告诉编辑器你要把obj当作NSString来使用，编辑器会给obj对象智能提醒和检测NSString的api，帮助你编写代码和语法检查，仅此而已。
```
5.常见的object-c的数据类型有那些， 和C的基本数据类型有什么区别？

```
1.常用OC类型：NSString、NSArray、NSDictionary、NSData、NSNumber等
2.OC对象需要手动管理内存，C的基本数据类型不需要管理内存
```
6.id 声明的变量有什么特性？

```
id声明的变量能指向任何OC对象。参照第4题，id声明的变量，相当于告诉编辑器，此变量的类型暂无具体类型，就是id类型，具体什么类型运行时来决定。
```
7.Objective-C如何对内存管理的,说说你的看法和解决方法?

```
内存管理机制是每个iOS开发者需要了解的。
1.每个对象都有一个引用计数器，每个新对象的计数器是1，当对象的计数器减为0时，就会被销毁
2.通过retain可以让对象的计数器+1、release可以让对象的计数器-1
3.还可以通过autorelease pool管理内存
4.如果用ARC，编译器会自动生成管理内存的代码
```
8.深拷贝和浅拷贝的区别？

```
深拷贝：拷贝内容，产生新的对象。
浅拷贝：拷贝指针，不会产生新的对象
```
9.使用分类有什么优点？分类和继承的区别？

```
分类可以在不修改原来类模型的基础上拓充方法
分类只能扩充方法、不能扩充成员变量；继承可以扩充方法和成员变量
继承会产生新的类
```
10.分类和扩展的区别？

```
分类是有名称的，类扩展没有名称
分类只能扩充方法、不能扩充成员变量；类扩展可以扩充方法和成员变量
类扩展一般就写在.m文件中，用来扩充私有的方法和成员变量（属性）
```
11.KVC和KVO是什么？

```
KVC是键值编码，可以通过一个字符串的key（属性名）修改对象的属性值
KVO是键值监听，可以监听一个对象属性值的改变
```
12.代理的目的是什么？

```
两个对象之间传递数据和消息
解耦，拆分业务逻辑
```
13.在Objective C中什么是mutable和immutable类型

```
mutable是可变类型，比如NSMutableArray，可以动态往里面添加元素
immutable是不可变类型，比如NSArray，固定的存储空间，不能添加元素
```
14.什么是单例？

```
单例：保证程序运行过程中，永远只有一个对象实例
目的是：全局共享一份资源、节省不必要的内存开销
```
15.
