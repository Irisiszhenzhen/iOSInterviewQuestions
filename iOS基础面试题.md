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
4.retain:set方法的实现是release旧值，retain新值，用于OC对象类型
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
常见单例：UIApplication、NSUserDefaults、NSNotificationCenter、NSFileManager、NSBundle等
```
15.什么时候使用NSMutableArray,什么时候使用NSArray？

```
当数组元素需要动态地添加或者删除时，用NSMutableArray
当数组元素固定不变时，用NSArray
```
16.什么情况下对象引用计数器会增加？

```
当做retain或者copy操作的时候，都有可能增加计数器
```
17.在Objective C中关键字atomic有什么作用？

```
atomic是原子性
atomic会对set方法的实现进行加锁
```
18.Objective C有私有方法和私有变量吗？

```
Objective C中没有private，public类似的关键字来控制类的权限，写在.h文件中，就是公共方法
在.m文件中声明和实现的方法，对编辑器来说就是私有的
```
19.堆和栈的区别？

```
堆空间的内存是动态分配的，一般存放对象，并且需要手动释放内存
栈空间的内存由系统自动分配，一般存放局部变量等，不需要手动管理内存
```
20.@property中有哪些属性关键字？

```
1.readwrite（默认）：同时生成get方法和set方法的声明和实现  
2.readonly：只生成get方法的声明和实现
3.assign（默认）：set方法的实现是直接赋值，用于基本数据类型
4.retain:set方法的实现是release旧值，retain新值，用于OC对象类型
5.copy：set方法的实现是release旧值，copy新值，用于NSString、block等类型
6.nonatomic：非原子性，set方法的实现不加锁（比atomic性能高）
7.atomic（默认）：原子性，会在set方法加上读写锁
8.weak：弱指针类型，一般用于UI控件、id类型
9、strong (就是retain)：强指针类型   一般用于OC对象类型，除 (UI控件、代理类型、字符串对象)
```
21.OC有多继承吗？没有的话用什么代替？

```
OC是单继承，没有多继承，有时可以使用分类和协议来代替多继承。
```
22.关键字const什么含义？

```
const int a;
int const a;
const int *a;
int const *a;
int * const a;
int const * const a;
```
```
1> 前两个的作用是一样：a 是一个不可修改的常整型数，只读
2> 第三、四个意味着 a 是一个指向常整型数的指针(整型数是不可修改的，但指针可以)
3> 第五个的意思：a 是一个指向整型数的常指针(指针指向的整型数是可以修改的，但指针是不可修改的)
4> 最后一个意味着：a 是一个指向常整型数的常指针(指针指向的整型数是不可修改的，同时指针也是不可修改的)
```
23.static的作用？

```
1.static修饰的函数是一个内部函数，只能在本文件中调用，其他文件不能调用
2. static修饰的全局变量是一个内部变量，只能在本文件中使用，其他文件不能使用
3. static修饰的局部变量只会初始化一次，并且在程序退出时才会回收内存
```
24.自动释放池常见面试代码

```
for (int i = 0; i < 10; ++i)
{   
      NSString *str = @"Hello World";   
      str = [str stringByAppendingFormat:@" - %d", i];   
      str = [str uppercaseString];    NSLog(@"%@", str);
}
```
问：以上代码存在什么样的问题？如果循环的次数非常大时，应该如何修改？

```
解决办法1：如果i比较大，可以用@autoreleasepool {}解决，放在for循环外，循环结束后，销毁创建的对象，解决占据栈区内存的问题
解决方法2：如果i玩命大，一次循环都会造成自动释放池被填满，自动释放池放在for循环内，每次循环都将上一次创建的对象release
```
25.@private、@protected、@public、@package类型的成员变量的作用域？

```
- @private：只能在当前类的对象方法中访问；
- @protected：可以在当前类以及子类的实现中直接访问，默认类型；
- @public：任何地方都可以直接访问对象的成员变量；
- @package：同一个“体系内”（框架）可以访问；
```
26.这个写法会出什么问题:`@property (copy) NSMutableArray *array;`?

```
@property 的setter方法设置成copy以后，array这个指针指向的是一个不可变数组，那么当使用点语法为给array赋值时，就会发生“unrecognized selector sent to instance”错误，程序就会崩溃。
```
27.调用对象的release 方法会销毁对象吗？

```
    不会，调用对象的release 方法只是将对象的引用计数器-1，当对象的引用计数器为0的时候会调用了对象的dealloc 方法才能进行释放对象的内存。
```
28.什么情况使用 weak 关键字，相比 assign 有什么不同？

```
1>.什么情况使用weak关键字?
- 在 ARC 中,在有可能出现循环引用的时候,往往要通过让其中一端使用 weak 来解决,比如: delegate 代理属性
- 自身已经对它进行一次强引用,没有必要再强引用一次,此时也会使用 weak,自定义 IBOutlet 控件属性一般也使用 weak；当然，也可以使用strong。
2>.weak与assign的不同?
- weak 此特质表明该属性定义了一种“非拥有关系” (nonowning relationship)。为这种属性设置新值时，设置方法既不保留新值，也不释放旧值。此特质同assign类似， 然而在属性所指的对象遭到摧毁时，属性值也会清空(nil out)。 而 assign 的“设置方法”只会执行针对“纯量类型” (scalar type，例如 CGFloat 或 NSlnteger 等)的简单赋值操作。
- assigin 可以用非 OC 对象,而 weak 必须用于 OC 对象
```

29.XIB与Storyboards的优缺点?

```
- XIB：在编译前就提供了可视化界面，可以直接拖控件，也可以直接给控件添加约束，更直观一些，而且类文件中就少了创建控件的代码，确实简化不少，通常每个XIB对应一个类。

- Storyboard：在编译前提供了可视化界面，可拖控件，可加约束，在开发时比较直观，而且一个storyboard可以有很多的界面，每个界面对应一个类文件，通过storybard，可以直观地看出整个App的结构。

- XIB：需求变动时，需要修改XIB很大，有时候甚至需要重新添加约束，导致开发周期变长。XIB载入相比纯代码自然要慢一些。对于比较复杂逻辑控制不同状态下显示不同内容时，使用XIB是比较困难的。当多人团队或者多团队开发时，如果XIB文件被发动，极易导致冲突，而且解决冲突相对要困难很多。

- Storyboard：需求变动时，需要修改storyboard上对应的界面的约束，与XIB一样可能要重新添加约束，或者添加约束会造成大量的冲突，尤其是多团队开发。对于复杂逻辑控制不同显示内容时，比较困难。当多人团队或者多团队开发时，大家会同时修改一个storyboard，导致大量冲突，解决起来相当困难。
```


