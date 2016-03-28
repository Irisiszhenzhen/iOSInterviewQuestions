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

20.能否向编译后得到的类中增加实例变量？能否向运行时创建的类中添加实例变量？为什么？

```
- 不能向编译后得到的类中增加实例变量；
- 能向运行时创建的类中添加实例变量；

解释如下：
因为编译后的类已经注册在 runtime 中，类结构体中的 objc_ivar_list 实例变量的链表 和 instance_size 实例变量的内存大小已经确定，同时runtime 会调用 class_setIvarLayout 或 class_setWeakIvarLayout 来处理 strong weak 引用。所以不能向存在的类中添加实例变量；
运行时创建的类是可以添加实例变量，调用 class_addIvar 函数。但是得在调用 objc_allocateClassPair 之后，objc_registerClassPair 之前，原因同上。

```
21.以+ scheduledTimerWithTimeInterval...的方式触发的timer，在滑动页面上的列表时，timer会暂定回调，为什么？如何解决？

```
RunLoop只能运行在一种mode下，如果要换mode，当前的loop也需要停下重启成新的。利用这个机制，ScrollView滚动过程中NSDefaultRunLoopMode（kCFRunLoopDefaultMode）的mode会切换到UITrackingRunLoopMode来保证ScrollView的流畅滑动：只能在NSDefaultRunLoopMode模式下处理的事件会影响ScrollView的滑动。

如果我们把一个NSTimer对象以NSDefaultRunLoopMode（kCFRunLoopDefaultMode）添加到主运行循环中的时候, ScrollView滚动过程中会因为mode的切换，而导致NSTimer将不再被调度。

同时因为mode还是可定制的，所以：

Timer计时会被scrollView的滑动影响的问题可以通过将timer添加到NSRunLoopCommonModes（kCFRunLoopCommonModes）来解决。代码如下：
//将timer添加到NSDefaultRunLoopMode中
 [NSTimer scheduledTimerWithTimeInterval: target: selector:@selector(timerTick:) userInfo: repeats:];
  //然后再添加到NSRunLoopCommonModes里
   NSTimer *timer = [NSTimer timerWithTimeInterval: target: selector:@selector(timerTick:) userInfo: repeats:];
    [[NSRunLoop currentRunLoop] addTimer:timer forMode:NSRunLoopCommonModes];
```

22.runloop和线程有什么关系？

```
总的说来，Run loop，正如其名，loop表示某种循环，和run放在一起就表示一直在运行着的循环。实际上，run loop和线程是紧密相连的，可以这样说run loop是为了线程而生，没有线程，它就没有存在的必要。Run loops是线程的基础架构部分， Cocoa 和 CoreFundation 都提供了 run loop 对象方便配置和管理线程的 run loop （以下都以 Cocoa 为例）。每个线程，包括程序的主线程（ main thread ）都有与之相应的 run loop 对象。

runloop 和线程的关系：
- 主线程的run loop默认是启动的。
iOS的应用程序里面，程序启动后会有一个如下的main()函数
( argc,  * argv[]) {
@autoreleasepool {
    return UIApplicationMain(argc, argv, , NSStringFromClass([AppDelegate class]));
}
}
重点是UIApplicationMain()函数，这个方法会为main thread设置一个NSRunLoop对象，这就解释了：为什么我们的应用可以在无人操作的时候休息，需要让它干活的时候又能立马响应。

- 对其它线程来说，run loop默认是没有启动的，如果你需要更多的线程交互则可以手动配置和启动，如果线程只是去执行一个长时间的已确定的任务则不需要。

- 在任何一个 Cocoa 程序的线程中，都可以通过以下代码来获取到当前线程的 run loop 。
NSRunLoop *runloop = [NSRunLoop currentRunLoop];
```

23.如何用GCD同步若干个异步调用？（如根据若干个url异步加载多张图片，然后在都下载完成后合成一张整图）

```
使用Dispatch Group追加block到Global Group Queue,这些block如果全部执行完毕，就会执行Main Dispatch Queue中的结束处理的block。

dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, );
dispatch_group_t group = dispatch_group_create();
dispatch_group_async(group, queue, ^{ /*加载图片1 */ });
dispatch_group_async(group, queue, ^{ /*加载图片2 */ });
dispatch_group_async(group, queue, ^{ /*加载图片3 */ });
dispatch_group_notify(group, dispatch_get_main_queue(), ^{
        // 合并图片
});
```

24.HTTP协议的特点，关于HTTP请求GET和POST的区别?

```
HTTP协议的特点:
- HTTP超文本传输协议，是短连接，是客户端主动发送请求，服务器做出响应，服务器响应之后，链接断开。HTTP是一个属于应用层面向对象的协议，HTTP有两类报文：请求报文和响应报文。
- HTTP请求报文：一个HTTP请求报文由请求行、请求头部、空行和请求数据4部分组成。
- HTTP响应报文：由三部分组成：状态行、消息报头、响应正文。
GET和POST的区别:
- GET请求：参数在地址后拼接，没有请求数据，不安全（因为所有参数都拼接在地址后面），不适合传输大量数据（长度有限制，为1024个字节）。
GET提交、请求的数据会附在URL之后，即把数据放置在HTTP协议头中。以？分割URL和传输数据，多个参数用&连接。如果数据是英文字母或数字，原样发送，如果是空格，转换为+，如果是中文/其他字符，则直接把字符串用BASE64加密。
- POST请求：参数在请求数据区放着，相对GET请求更安全，并且数据大小没有限制。把提交的数据放置在HTTP包的包体中.
- GET提交的数据会在地址栏显示出来，而POST提交，地址栏不会改变。

传输数据的大小：
- GET提交时，传输数据就会受到URL长度限制，POST由于不是通过URL传值，理论上书不受限。
- POST的安全性要比GET的安全性高；
- 通过GET提交数据，用户名和密码将明文出现在URL上，比如登陆界面有可能被浏览器缓存。
```

25.如何理解MVC设计模式?

```
MVC是一种架构模式，M表示MOdel，V表示视图View，C表示控制器Controller：
- Model负责存储、定义、操作数据；
- View用来展示书给用户，和用户进行操作交互；
- Controller是Model和View的协调者，Controller把Model中的数据拿过来给View用。Controller可以直接与Model和View进行通信，而View不能和Controller直接通信。View与Controller通信需要利用代理协议的方式，当有数据更新时，Model也要与Controller进行通信，这个时候就要用Notification和KVO，这个方式就像一个广播一样，MOdel发信号，Controller设置监听接受信号，当有数据更新时就发信号给Controller，Model和View不能直接进行通信，这样会违背MVC设计模式。
 ```
 
 26.线程与进程的区别和联系?
 
 ```
 - 一个程序至少要有进城,一个进程至少要有一个线程。
 
- 进程:资源分配的最小独立单元,进程是具有一定独立功能的程序关于某个数据集合上的一次运行活动,进程是系统进行资源分配和调度的一个独立单位。
- 线程:进程下的一个分支,是进程的实体,是CPU调度和分派的基本单元,它是比进程更小的能独立运行的基本单位,线程自己基本不拥有系统资源,只拥有一点在运行中必不可少的资源(程序计数器、一组寄存器、栈)，但是它可与同属一个进程的其他线程共享进程所拥有的全部资源。

- 进程和线程都是由操作系统所体会的程序运行的基本单元，系统利用该基本单元实现系统对应用的并发性。
- 进程和线程的主要差别在于它们是不同的操作系统资源管理方式。进程有独立的地址空间，一个进程崩溃后，在保护模式下不会对其它进程产生影响，而线程只是一个进程中的不同执行路径。线程有自己的堆栈和局部变量，但线程之间没有单独的地址空间，一个线程死掉就等于整个进程死掉，所以多进程的程序要比多线程的程序健壮，但在进程切换时，耗费资源较大，效率要差一些。
- 但对于一些要求同时进行并且又要共享某些变量的并发操作，只能用线程，不能用进程。
 ```
 
 27.
 
 ```
 
 ```