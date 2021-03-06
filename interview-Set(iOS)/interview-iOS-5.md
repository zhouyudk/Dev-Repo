# interview-iOS - 5

# Question list

- 对数组中的元素去重复 (四种)
-  请简单写出增、删、改、查的SQL语句
- 与 NSURLConnection 相比，NSURLsession 改进哪些?
- 使用drawRect有什么影响？
- 什么时候会报unrecognized selector的异常？如何避免?
-  界面多个网络请求,如何处理刷新的?
-  如果tableView界面网络请求有缓存数据逻辑?
-  init方法私有化?
-  线程中栈与堆是公有的还是私有的 ?
# 宝库iOS开发笔试题

## 对数组中的元素去重复 (2017年初面试北京奥鹏在线教育笔试题)

- 参考如下代码

```
NSArray *array = @[@"12-11", @"12-11", @"12-11", @"12-12", @"12-13", @"12-14"];

1.开辟新的内存空间，然后判断是否存在，若不存在则添加到数组中，得到最终结果的顺序不发生变化。效率分析：时间复杂度为O ( n2 )：

NSMutableArray *resultArray = [[NSMutableArray alloc] initWithCapacity:array.count];
// 外层一个循环
for (NSString *item in array) {
   // 调用-containsObject:本质也是要循环去判断，因此本质上是双层遍历
   // 时间复杂度为O ( n^2 )而不是O (n)
    if (![resultArray containsObject:item]) {
      [resultArray addObject:item];
    }
}
NSLog(@"resultArray: %@", resultArray);
 
2.利用NSDictionary去重，字典在设置key-value时，若已存在则更新值，若不存在则插入值，然后获取allValues。若不要求有序，则可以采用此种方法。若要求有序，还得进行排序。效率分析：只需要一个循环就可以完成放入字典，若不要求有序，时间复杂度为O(n)。若要求排序，则效率与排序算法有关：
 NSMutableDictionary *resultDict = [[NSMutableDictionary alloc] initWithCapacity:array.count];
for (NSString *item in array) {
    [resultDict setObject:item forKey:item];
}
NSArray *resultArray = resultDict.allValues;
NSLog(@"%@", resultArray);

如果需要按照原来的升序排序，可以这样：
resultArray = [resultArray sortedArrayUsingComparator:^NSComparisonResult(id  _Nonnull obj1, id  _Nonnull obj2) {
  NSString *item1 = obj1;
  NSString *item2 = obj2;
  return [item1 compare:item2 options:NSLiteralSearch];
}];
NSLog(@"%@", resultArray);
 
3.利用集合NSSet的特性(确定性、无序性、互异性)，放入集合就自动去重了。但是它与字典拥有同样的无序性，所得结果顺序不再与原来一样。如果不要求有序，使用此方法与字典的效率应该是差不多的。效率分析：时间复杂度为O (n)：
NSSet *set = [NSSet setWithArray:array];
NSArray *resultArray = [set allObjects];
NSLog(@"%@", resultArray);
如果要求有序，那就得排序，比如这里要升序排序：

resultArray = [resultArray sortedArrayUsingComparator:^NSComparisonResult(id  _Nonnull obj1, id  _Nonnull obj2) {
  NSString *item1 = obj1;
  NSString *item2 = obj2;
  return [item1 compare:item2 options:NSLiteralSearch];
}];
NSLog(@"%@", resultArray);
4. 有序集合--->直接使用有序集合
NSOrderedSet *set = [NSOrderedSet orderedSetWithArray:array];
NSLog(@"%@", set.array);

```

## 请简单写出增、删、改、查的SQL语句
- 增：`insert into tb_blogs(name, url) values('DragonLi','https://github.com/DevDragonLi');`
 
- 删：	`delete from tb_blogs where blogid = 1;`
 
- 改：`update tb_blogs set url = 'www.textURL.com' where blogid = 1;`
 
- 查：`select name, url from tb_blogs where blogid = 1;`


## 与 NSURLConnection 相比，NSURLsession 改进哪些?
- 可以配置每个 session 的缓存，协议，cookie，以及证书策略（credential policy），甚至跨程序共享这些信息
- session task。它负责处理数据的加载以及文件和数据在客户端与服务端之间的上传和下载。NSURLSessionTask 与 NSURLConnection 最大的相似之处在于它也负责数据的加载，最大的不同之处在于所有的 task 共享其创造者 NSURLSession 这一公共委托者（common delegate）


## 使用drawRect有什么影响？
- drawRect方法依赖Core Graphics框架来进行自定义的绘制
	- 缺点：它处理touch事件时每次按钮被点击后，都会用setNeddsDisplay进行强制重绘；而且不止一次，每次单点事件触发两次执行。这样的话从性能的角度来说，对CPU和内存来说都是欠佳的。特别是如果在我们的界面上有多个这样的UIButton实例，那就会很糟糕了
	- 这个方法的调用机制也是非常特别. 当你调用 setNeedsDisplay 方法时, UIKit 将会把当前图层标记为dirty,但还是会显示原来的内容,直到下一次的视图渲染周期,才会将标记为 dirty 的图层重新建立Core Graphics上下文,然后将内存中的数据恢复出来, 再使用 CGContextRef 进行绘制

	
## 什么时候会报unrecognized selector的异常？如何避免?
- 当调用该对象上某个方法,而该对象上没有实现这个方法的时候， 可以通过“消息转发”进行解决，如果还是不行就会报unrecognized selector异常
- objc是动态语言，每个方法在运行时会被动态转为消息发送，即：objc_msgSend(receiver, selector)，整个过程介绍如下：
	- objc在向一个对象发送消息时，runtime库会根据对象的isa指针找到该对象实际所属的类然后在该类中的方法列表以及其父类方法列表中寻找方法运行
如果，在最顶层的父类中依然找不到相应的方法时，程序在运行时会挂掉并抛出异常unrecognized selector sent to XXX 。但是在这之前，objc的运行时会给出三次拯救程序崩溃的机会

- 三次拯救程序崩溃的机会

	- Method resolution:objc运行时会调用+resolveInstanceMethod:或者 +resolveClassMethod:，让你有机会提供一个函数实现。
如果你添加了函数并返回 YES，那运行时系统就会重新启动一次消息发送的过程
如果 resolve 方法返回 NO ，运行时就会移到下一步，消息转发
	- Fast forwarding:如果目标对象实现了-forwardingTargetForSelector:，Runtime 这时就会调用这个方法，给你把这个消息转发给其他对象的机会
只要这个方法返回的不是nil和self，整个消息发送的过程就会被重启，当然发送的对象会变成你返回的那个对象。否则，就会继续Normal Fowarding。这里叫Fast，只是为了区别下一步的转发机制。因为这一步不会创建任何新的对象，但Normal forwarding转发会创建一个NSInvocation对象，相对Normal forwarding转发更快点，所以这里叫Fast forwarding
	- Normal forwarding
这一步是Runtime最后一次给你挽救的机会。
首先它会发送-methodSignatureForSelector:消息获得函数的参数和返回值类型。
如果-methodSignatureForSelector:返回nil，Runtime则会发出-doesNotRecognizeSelector:消息，程序这时也就挂掉了。
如果返回了一个函数签名，Runtime就会创建一个NSInvocation对象并发送-forwardInvocation:消息给目标对象


## 界面多个网络请求,如何处理刷新的?

- 使用调度组:**问题 : 为多个请求均加载完成，但界面已在未得到数据前提前刷新导致界面空白**
	- dispatch_semaphore信号量为基于计数器的一种多线程同步机制。用于解决在多个线程访问共有资源时候，会因为多线程的特性而引发数据出错的问题。

	- semaphore计数大于等于1，计数-1，返回，程序继续运行。如果计数为0，则等待。

	- dispatch_semaphore_signal(semaphore)为计数+1操作。
	
	-  dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER)为设置等待时间，这里设置的等待时间是一直等待


```
1. 调度组
dispatch_group_t group = dispatch_group_create();
    
    dispatch_group_async(group, dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        NSLog(@"netWorking_Frist");
        
    });
    
    dispatch_group_async(group, dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        NSLog(@"netWorking_Second");
    });
    dispatch_group_async(group,dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        NSLog(@"netWorking_Three");
    });
    
    dispatch_group_notify(group, dispatch_get_main_queue(), ^{
        NSLog(@"complete");
    });
    
        
2. 信号量

- (void)netWorkingimplementation{ // 基于自身项目的网络请求
//    1. 发起请求
    dispatch_semaphore_t  semaphore = dispatch_semaphore_create(0);
//    2. 成功/失败回调标记
    dispatch_semaphore_signal(semaphore);
//    3. 计数为0 ,则一直等待
    dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER);
}
    
```


## 如果tableView界面网络请求有缓存数据逻辑?

- 首次进入一个界面tableview的数据应该先读取缓存，主要防止没有网络或者网络不好的情况下用户等待数据时间过长或者没有数据用户体验不好，主要用于某些tableview请求的数据量很大，或者数据短时间内变化不是很快的地方。
- 用户下拉刷新表示重新请求数据，应该强制更新数据，不论本地是否有缓存,但是这部分数据应该存入数据库，保证退出后下次进入界面的时候能够读取最后更新的缓存。
- 上拉加载更多的时候不读缓存，因为如果客户端自己在处理这部分逻辑比较复杂，不是说实现起来复杂，因为对于本地存储来说调用的是统一的一个方法，主要是因为如果用户下拉刷新需要强制更新数据，不论本地有无缓存，都要从服务器请求最新数据。那么问题来了，如果我把用户上拉加载更多时候的数据也存入本地，假设用户上拉加载了5页数据，然后又强制下拉刷新了一下，这个时候tableview显示的是最新的第一页数据，如果接着上拉加载更多我应该是读取缓存还是直接发起网络请求呢。毫无疑问应该直接发起请求，因为下拉强制刷新已经导致了第一页为最新数据，如果第2-5页数据缓存没有过期并且服务器数据确实变化了的话，客户端将得不到新的数据，所以干脆仅仅第一次进入界面tableview请求第一页数据的时候要读缓存。
- 根本不用缓存的地方主要是某些数据变化非常快，或者发起的网络请求是一次性的操作，比如收藏某个题目等等。


## init方法私有化
	
```
1 .h 
- (instancetype)init __attribute__((unavailable("Disabled. Use +sharedInstance instead")));
- (instancetype)init NS_UNAVAILABLE;

2.m
//3. 内部不响应 不建议!
- (instancetype)init {
   // 抛出不识别,没有说明真的原因
    [super doesNotRecognizeSelector:_cmd];
    return nil;
}

// 通过断言
- (instancetype)init1{
    NSAssert(false,@"unavailable, use sharedInstance instead");
    return nil;
}

// 通过异常
- (instancetype)init2{
    [NSException raise:NSGenericException format:@"Disabled. Use +[%@ %@] instead",
     NSStringFromClass([self class]),
     NSStringFromSelector(@selector(sharedInstance))];
    
    return nil;
}

```	 
## 线程中栈与堆是公有的还是私有的 ?
- 栈私有, 堆公有  






