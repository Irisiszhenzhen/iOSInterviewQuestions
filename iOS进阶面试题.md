# iOS进阶面试题
1、KVC的底层实现？

```
当一个对象调用setValue方法时，方法内部会做以下操作：
①检查是否存在相应key的set方法，如果存在，就调用set方法
②如果set方法不存在，就会查找与key相同名称并且带下划线的成员属性，如果有，则直接给成员属性赋值
③如果没有找到_key,就会查找相同名称的属性key，如果有就直接赋值
④如果还没找到，则调用valueForUndefinedKey:和setValue:forUndefinedKey:方法。这些方法的默认实现都是抛出异常，我们可以根据需要重写它们。
```
2、__block和__weak修饰符的区别？

```
1.__block不管是ARC还是MRC模式下都可以使用，可以修饰对象，还可以修饰基本数据类型。
2.__weak只能在ARC模式下使用，也只能修饰对象（NSString），不能修饰基本数据类型（int）。
3.__block对象可以在block中被重新赋值，__weak不可以。
```
3、block和代理的区别，哪个更好？

```
代理回调更面向过程，block更面向结果。如果需要在执行的不同步骤时被通知，你就要使用代理。如果只需要请求的消息或者失败的详情，应该使用block。block更适合与状态无关的操作，比如被告知某些结果，block之间是不会相互影响的。但是代理更像一个生产流水线，每个回调方法是生产线上的一个处理步骤，一个回调的变动可能会引起另一个回调的变动。
要是一个对象有超过一个的不同事件，应该使用代理。一个对象只有一个代理，要是某个对象是个单例对象，就不能使用代理。要是一个对象调用方法需要返回一些额外的信息，就可能需要使用代理。
```
4、Object C中创建线程的方法是什么?如果在主线程中执行代码，方法是什么?如果想延时执行代码、方法又是什么?

```
线程创建有三种方法：使用NSThread创建、使用GCD的dispatch、使用子类化的NSOperation,然后将其加入NSOperationQueue;

NSThread创建线程的三种方法：
 NSThread *thread = [[NSThread alloc]initWithTarget:self selector:@selector(run:) object:@"nil"];
 [NSThread detachNewThreadSelector:@selector(run:) toTarget:self withObject:@"我是分离出来的子线程"];
[self performSelectorInBackground:@selector(run:) withObject:@"我是后台线程"];

在主线程执行代码，就调用performSelectorOnMainThread方法。

如果想延时执行代码可以调用performSelector:onThread:withObject:waitUntilDone:方法；

GCD：
利用异步函数dispatch_async()创建子线程。

在主线程执行代码，dispatch_async(dispatch_get_main_queue(), ^{});

延迟执行代码（延迟·可以控制代码在哪个线程执行）：
dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(2.0 * NSEC_PER_SEC)), dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{});

NSOperationQueue：
使用NSOperation的子类封装操作，再将操作添加到NSOperationQueue创建的队列中，实现多线程。
在主线程执行代码，只要将封装代码的NSOperation对象添加到主队列就可以了。
```
5、控制器view的生命周期？

```
控制器view是懒加载，用到的时候才加载。
生命周期方法调用顺序：
loadView-->viewDidLoad-->viewWillAppear-->viewDidAppear-->viewWillDisappear-->viewDidDisappear-->didReceiveMemoryWarning(收到内存警告)-->viewWillUnload-->viewDidUnload
```
6、控制器View的加载过程？

```
当程序访问了控制器的View属性时会先判断控制器的View是否存在，如果存在就直接返回已经存在的View；如果不存在，就会先调用loadView这个方法；如果控制器的loadView方法实现了，就会按照loadView方法加载自定义的View；如果控制器的loadView方法没有实现就会判断storyboard是否存在；如果storyboard存在就会按照storyboard加载控制器的View；如果storyboard不存在，就会创建一个空视图返回。
```

7、iOS开发中数据存储的方法？

```
常见存储方法有三种：plist存储、偏好设置(NSUserDefaults)和归档。
属性列表（plist)存储：
适用对象：只有带有writeToFil方法的对象才能用这种方法，比如NSString、、NSArray、NSDictionary、NSSet、NSNumber、NSData等，不能存储自定义的对象
存储方法：
调用对象的writeToFile...方法就可以写入文件
读取方法：
调用对象的...WithContentsOfFile方法就可以从文件中读取对象内容

偏好设置(NSUserDefaults):
适用对象：使用NSUserDefaults，存储用户关于应用的偏好设置，本质上仍然是plist存储，不能存储自定义的OC对象
存储方法：
利用NSUserDefaults的setObject等方法进行存储
读取方法：
利用NSUserDefaults的objectForKey等方法进行读取

归档(NSKeyedArchiver):
适用对象：可以存储自定义的对象，只有遵守了NSCoding协议的对象才可以
存储方法（归档）：
调用NSKeyedArchiver的archiverRootObject: toFile: 方法存储对象，archiverRootObject执行这个方法时，底层就会调用要存的对象的encodeWithCoder方法，调用encodeWithCoder目的就是想询问下要存对象的哪些属性
读取方法（解档）：
调用NSKeyedArchiver的unarchiverObjectWithFile:方法，执行enachiverObjectWithFile:方法时就会调用initWithCoder：方法，调用initWithCoder：方法目的就是询问下要读的对象有那些属性要读，怎么读。
注意：
当有继承关系时，必须调用父类的归档解档方法，当有组合包含关系时，也必须实现所包含对象类的归档解档方法。
```

8、为什么很多内置类如UITableViewController的delegate属性都是weak而不是strong？

```
会引起循环引用的问题。
UITableViewController内部有一个强指针tableView属性，tableView指针指向一个UITableView，UITableView内部有一个强指针subviews属性，指向一个装着UITableView全部的子控件的强指针数组，数组中又有强指针指向UITableView中存在的子控件。tableView内部有一个指针delegate，tableView的delegate就是控制器本身，二者相互引用，如果delegate属性是strong就会引起循环应用，造成内存泄露，因此必须有一个对象是弱指针，所以delegate是weak。
```
9、UI控件为什么不用strong用weak？

```
控制器有个强指针View属性，View属性指向内存中的一个UIView，UIView内部有一个强指针subviews属性，指向一个装着UIView全部的子控件的强指针数组，数组中又有强指针指向UIView中存在的子控件。所以，只要控制器在，View就在，View中的子控件就在，所以，ui控件没必要用强指针，用weak就可以。
```
10、block使用时的注意点？

```
Block可以使用在定义之前声明的局部变量。
int i = 10;
void(^myBlock)() = ^{
    NSLog(@"%d", i);
};
i = 100;
myBlock();
注意：
在定义Block时，会在Block中建立当前局部变量内容的副本(拷贝)。
后续再对该变量的数值进行修改，不会影响Block中的数值。
以上代码输出结果为10。
如果需要在block中保持局部变量的数值变化，需要使用__block关键字。使用__block关键字后，同样可以在Block中修改该变量的数值。
将第一行代码改为： __block int i = 10;后，输出结果就变成了100；
另外：block代码块中不能直接用点语法调用self的方法，会造成循环引用，要用中括号调用。
```
11、如何对UITableView进行优化？

```
UITableViewCell的重用原理：
当滚动列表时，部分UITableViewCell会移出窗口，UITableView会将窗口外的UITableViewCell放入一个对象池中，等待重用。当UITableView要求dataSource返回UITableViewCell时，dataSource会先查看这个对象池，如果池中有未使用的UITableViewCell，dataSource会用新的数据配置这个UITableViewCell，然后返回给UITableView，重新显示到窗口中，从而避免创建新对象。
还有一个非常重要的问题：有时候需要自定义UITableViewCell（用一个子类继承UITableViewCell），而且每一行用的不一定是同一种UITableViewCell（如短信聊天布局），所以一个UITableView可能拥有不同类型的UITableViewCell，对象池中也会有很多不同类型的UITableViewCell，时可能会得到错误类型的UITableViewCell那么UITableView在重用UITableViewCell。解决方案：UITableViewCell有个NSString *reuseIdentifier属性，可以在初始化UITableViewCell的时候传入一个特定的字符串标识来设置reuseIdentifier（一般用UITableViewCell的类名）。当UITableView要求dataSource返回UITableViewCell时，先通过一个字符串标识到对象池中查找对应类型的UITableViewCell对象，如果有，就重用，如果没有，就传入这个字符串标识来初始化一个UITableViewCell对象。
```

12、UIView动画与核心动画的区别？

```
UIView动画与核心动画的区别：
1、核心动画只作用在CALayer上面，UIView是没有办法使用核心动画的
2、核心动画看到的都是假象，并没有修改UIView的真实位置

什么时候使用UIView动画？
需要与用户进行交互的时候使用UIView动画，如果不需要与用户进行交互，两者都可以使用。

什么时候使用核心动画？
1、根据路径做动画，要使用核心动画（帧动画）
2、做转场动画时，也要使用核心动画
```
13、自定义视图中重写layoutsubView需要调用父类的layoutsubView吗，为什么？

```
如果重写的控件是UIView不调用父类的layoutsubView也没关系，里面没有任何子控件，所以不会做什么事情。一般系统自带视图中有子控件的都会重写layoutSubviews方法，因此我们自定义系统自带控件并且重写layoutSubviews必须调用[super layoutSubviews],先布局系统自带子控件的位置和尺寸，才设置我们自己的控件位置和尺寸。否则会发现想用系统自带视图的子控件的时候，会出现意想不到的效果。
```
14、应用程序的启动流程？

```
1.执行Main
2.执行UIApplicationMain函数.
3.创建UIApplication对象,并设置UIApplicationMain对象的代理.
  UIApplication的第三个参数就是UIApplication的名称,如果指定为nil,它会默认为UIApplication.
  UIApplication的第四个参数为UIApplication的代理.
4.开启一个主运行循环.保证应用程序不退出.
5.加载info.plist.加载配置文件.判断一下info.plist文件当中有没有Main storyboard file base name里面有没有指定storyboard文件,如果有就去加载info.plist文件,如果没有,那么应用程序加载完毕.
```
15、NSString 的时候用copy和strong的区别？

```
OC中NSString为不可变字符串的时候，用copy和strong都是只分配一次内存，但是如果用copy的时候，需要先判断字符串是否是不可变字符串，如果是不可变字符串，就不再分配空间，如果是可变字符串才分配空间。如果程序中用到NSString的地方特别多，每一次都要先进行判断就会耗费性能，影响用户体验，用strong就不会再进行判断，所以，不可变字符串可以直接用strong。
```
16、事件传递与响应的完整过程?

```
在产生一个事件时,系统会将该事件加入到一个由UIApplication管理的事件队列中,
UIApplication会从事件队列中取出最前面的事件,将它传递给先发送事件给应用程序的主窗口.
主窗口会调用hitTest方法寻找最适合的视图控件,找到后就会调用视图控件的touches方法来做具体的事情.
当调用touches方法,它的默认做法, 就会将事件顺着响应者链条往上传递，
传递给上一个响应者,接着就会调用上一个响应者的touches方法
```
17.ASIHttpRequest、AFNetWorking之间的区别?

```
- ASIHttpRequest功能强大，主要是在MRC下实现的，是对系统CFNetwork API进行了封装，支持HTTP协议的CFHTTP，配置比较复杂，并且ASIHttpRequest框架默认不会帮你监听网络改变，如果需要让ASIHttpRequest帮你监听网络状态改变，并且手动开始这个功能。

- AFNetWorking构建于NSURLConnection、NSOperation以及其他熟悉的Foundation技术之上。拥有良好的架构，丰富的API及模块构建方式，使用起来非常轻松。它基于NSOperation封装的，AFURLConnectionOperation子类。

- ASIHttpRequest是直接操作对象ASIHttpRequest是一个实现了NSCoding协议的NSOperation子类；AFNetWorking直接操作对象的AFHttpClient，是一个实现NSCoding和NSCopying协议的NSObject子类。

- 同步请求：ASIHttpRequest直接通过调用一个startSynchronous方法；AFNetWorking默认没有封装同步请求，如果开发者需要使用同步请求，则需要重写getPath:paraments:success:failures方法，对于AFHttpRequestOperation进行同步处理。

- 性能对比：AFNetworking请求优于ASIHttpRequest；
```

18.如何进行真机调试？

```
1.首先需要用钥匙串创建一个钥匙（key）；
2.将钥匙串上传到官网，获取iOS Development证书；
3.创建App ID即我们应用程序中的Boundle ID；
4.添加Device ID即UDID；
5.通过勾选前面所创建的证书：App ID、Device ID；
6.生成mobileprovision文件；
7.先决条件：申请开发者账号 99美刀
```

19.APP发布的上架流程?

```
1.登录应用发布网站添加应用信息；
2.下载安装发布证书；
3.选择发布证书，使用Archive编译发布包，用Xcode将代码（发布包）上传到服务器；
4.等待审核通过;
5.生成IPA：菜单栏->Product->Archive.
```
