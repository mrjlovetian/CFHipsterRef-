

# Objective-C Runtime

<div class="post-body" itemprop="articleBody">

![](http://7xjxaq.com1.z0.glb.clouddn.com/github/blog/CFHipsterRef_Chapter_2_Objective-C_Runtime.png)

# 0x00 前言

这篇文章是自己在学习 [CFHipsterRef](https://gum.co/cfhipsterref) Chapter 2 Objective-C Runtime 时做的笔记。

<a id="more"></a>

# 0x01 libobjc

`libobjc` 是 Objective-C 2.0 运行时的共享库。

要想使用 runtime，可以导入 `<objc/runtime.h>` 头文件。

# 0x02 发送消息（Message Sending）

在 Objective-C 中，调用一个对象的某个方法，一般的写法是这样的：



 

<pre><span class="line">[object message];</span>
</pre>


</figure>

然后编译器最后会将该语句翻译成：

<figure class="highlight objectivec">


<pre><span class="line">objc_msgSend(object, <span class="keyword">@selector</span>(message));</span>
</pre>


</figure>

`objc_msgSend` 有几种形式：

*   `objc_msgSend`：向一个类发送简单返回值类型的消息
*   `objc_msgSend_stret`：向一个类发送返回值类型为结构体的消息
*   `objc_msgSendSuper`：向父类发送简单返回值类型的消息
*   `objc_msgSendSuper_stert`：向父类发送返回值类型为结构体的消息

# 0x03 属性元编程（Metaprogramming with Properties）

属性定义了对象状态的公共接口。

例如，访问一个对象的属性列表，我们可以通过属性元编程来避免手动实现 `NSCoding`：
 | 

<pre><span class="line"><span class="preprocessor">#pragma mark - NSCoding</span></span>
<span class="line"></span>
<span class="line">- (<span class="keyword">id</span>)initWithCoder:(<span class="built_in">NSCoder</span> *)decoder {</span>
 <span class="line"><span class="keyword">if</span> (<span class="keyword">self</span> = [<span class="keyword">super</span> init]) {</span>
 <span class="line"><span class="keyword">unsigned</span> <span class="keyword">in</span> count;</span>
 <span class="line">objc_property_t *properties = class_copyPropertyList([<span class="keyword">self</span> class], &count);</span>
 <span class="line"><span class="keyword">for</span> (<span class="built_in">NSUInteger</span> i = <span class="number">0</span>; i < count; i++) {</span>
 <span class="line">objc_property_t property = properties[i];</span>
 <span class="line"><span class="built_in">NSString</span> *key = [<span class="built_in">NSString</span> stringWithUTF8String:property_getName(property)];</span>
 <span class="line">[<span class="keyword">self</span> setValue:[decoder decodeObjectForKey:key] forKey:key];</span>
 <span class="line">}</span>
 <span class="line"></span> 
 <span class="line">free(properties);</span>
 <span class="line">}</span>
 <span class="line"></span> 
 <span class="line"><span class="keyword">return</span> <span class="keyword">self</span>;</span>
<span class="line">}</span>
<span class="line"></span>
<span class="line">- (<span class="keyword">void</span>)encodeWithCoder:(<span class="built_in">NSCoder</span> *)coder {</span>
 <span class="line"><span class="keyword">unsigned</span> <span class="keyword">int</span> count;</span>
 <span class="line">objc_property_t *properties = class_getPropertyList([<span class="keyword">self</span> class], &count);</span>
 <span class="line"><span class="keyword">for</span> (<span class="built_in">NSUInteger</span> i = <span class="number">0</span>; i < count; i++) {</span>
 <span class="line">objc_property_t property = properties[i];</span>
 <span class="line"><span class="built_in">NSString</span> *key = [<span class="built_in">NSString</span> stringWithUTF8String:property_getName(property)];</span>
 <span class="line">[coder encodeObject:[<span class="keyword">self</span> valueForKey:key] forKey:key];</span>
 <span class="line">}</span>
 <span class="line"></span> 
 <span class="line">free(properties);</span>
<span class="line">}</span>
</pre>


</figure>

# 0x04 关联对象（Associated Objects）

关联对象是 Objective-C 运行时的一个特性，允许开发者对已经存在的类在扩展中添加自定义的属性，这非常有用。

关联对象使用非常简单，用 `objc_setAssociatedObject` 存储一个关联值，用 `objc_getAssociatedObject` 获取这个关联值。

任何关联对象都是通过一个 key 来关联的，这个 key 可以是任何常量值。最简单的方法就是传入 getter 方法选择器（方法选择器在运行时保证是唯一且不变的）。

<figure class="highlight objectivec">

<pre><span class="line"><span class="class"><span class="keyword">@interface</span> <span class="title">NSObject</span> (<span class="title">AssociatedObject</span>)</span></span>
<span class="line"></span>
<span class="line"><span class="keyword">@property</span> (<span class="keyword">strong</span>, <span class="keyword">nonatomic</span>) <span class="keyword">id</span> associatedObject;</span>
<span class="line"></span>
<span class="line"><span class="keyword">@end</span></span>
<span class="line"></span>
<span class="line"><span class="class"><span class="keyword">@implementation</span> <span class="title">NSObject</span> (<span class="title">AssociatedObject</span>)</span></span>
<span class="line"></span>
<span class="line">- (<span class="keyword">void</span>)setAssociatedObject:(<span class="keyword">id</span>)object {</span>
 <span class="line">objc_setAssociatedObject(<span class="keyword">self</span>, <span class="keyword">@selector</span>(associatedObject), object, OBJC_ASSO<span class="built_in">CIATION_RETAIN_NONATOMIC</span>);</span>
<span class="line">}</span>
<span class="line"></span>
<span class="line">- (<span class="keyword">id</span>)associatedObject {</span>
 <span class="line"><span class="keyword">return</span> objc_getAssociatedObject(<span class="keyword">self</span>, <span class="keyword">@selector</span>(associatedObject));</span>
<span class="line">}</span>
</pre>

 

</figure>

# 0x05 动态添加方法（Dynamically Adding a Method）

正如属性描述了一个对象的状态，方法则构成了对象的行为。

在 Objective-C 中，方法以 `+` 或 `-` 前置声明，表示该方法是类方法还是实例方法（也就是说是通过类调用的还是类的对象来调用的）。

一个类可以通过创建一个 category 来添加新的方法，然后可以在包含了这个 category 的地方使用这些新的方法。

不过，方法也可以在运行时创建：

<figure class="highlight objectivec">


<pre><span class="line">Class c = [<span class="built_in">NSObject</span> class];</span>
<span class="line">IMP addingMethodIMP = imp_implementationWithBlock((<span class="built_in">NSString</span> *)^(<span class="keyword">id</span> <span class="keyword">self</span>, <span class="keyword">id</span> arg1) {</span>
 <span class="line"><span class="keyword">return</span> [<span class="built_in">NSString</span> stringWithFormat:<span class="string">@"arg1 = %@"</span>, arg1];</span>
<span class="line">});</span>
<span class="line"></span>
<span class="line"><span class="keyword">const</span> <span class="keyword">char</span> *addingMethodTypes = [[<span class="built_in">NSString</span> stringWithFormat:<span class="string">@"%s%s%s"</span>, @encode(<span class="keyword">id</span>), @encode(<span class="keyword">id</span>), @encode(SEL)] UTF8String];</span>
<span class="line">class_addMethod(c, <span class="keyword">@selector</span>(addingMethodWithArg:), addingMethodIMP, addingMethodTypes);</span>
</pre>

 

</figure>

# 0x06 Method Swizzling

Method Swizzling 是改变一个已存在的方法的实现的过程。这个技术是改变一个映射到一个类的调度表中的底层函数的方法，在运行时修改为调用自己定义的方法。

比如在某些时候想要跟踪一个应用里面的每个 ViewController 在一次生命周期内打开的次数。想要实现这个功能有几种方法：

1.  每个 ViewController 都添加跟踪代码到重载的 `viewDidAppear` 方法里面，但是这样会有很多重复的代码。
2.  子类化 UIViewController，但是这样的话，还要子类化 `UITableViewController`、`UINavigationController` 等其他用到的 ViewController，还是要添加很多重复的代码。

有了 Method Swizzling，解决办法就非常优雅了：

<figure class="highlight objectivec">


<pre><span class="line"><span class="preprocessor">#import <span class="title"><objc/runtime.h></span></span></span>
<span class="line"></span>
<span class="line"><span class="class"><span class="keyword">@implementation</span> <span class="title">UIViewController</span> (<span class="title">Tracking</span>)</span></span>
<span class="line"></span>
<span class="line">+ (<span class="keyword">void</span>)load {</span>
 <span class="line"><span class="keyword">static</span> <span class="built_in">dispatch_once_t</span> onceToken;</span>
 <span class="line"><span class="built_in">dispatch_once</span>(&onceToken, ^{</span>
 <span class="line">Class class = [<span class="keyword">self</span> class];</span>
 <span class="line"></span> 
 <span class="line">SEL originalSelector = <span class="keyword">@selector</span>(viewDidAppear:);</span>
 <span class="line">SEL swizzledSelector = <span class="keyword">@selector</span>(xxx_viewDidAppear:);</span>
 <span class="line"></span> 
 <span class="line">Method originalMethod = class_getInstanceMethod(class, originalSelector);</span>
 <span class="line">Method swizzledMethod = class_getInstanceMethod(class, swizzledSelector);</span>
 <span class="line"></span> 
 <span class="line"><span class="built_in">BOOL</span> didAddMethod = class_addMethod(class, originalSelector, method_getImplementation(swizzledMthod), method_getTypeEncoding(swizzledMethod));</span>
 <span class="line"><span class="keyword">if</span> (didAddMethod) {</span>
 <span class="line">class_replaceMethod(class, swizzledSelector, method_getImplementation(originalMethod), method_getTypeEncoding(originalMethod));</span>
 <span class="line">} <span class="keyword">else</span> {</span>
 <span class="line">method_exchangeImplementations(originalMethod, swizzledMethod);</span>
 <span class="line">}</span>
 <span class="line">});</span>
<span class="line">}</span>
<span class="line"></span>
<span class="line"><span class="preprocessor">#pragma mark - Method Swizzling</span></span>
<span class="line"></span>
<span class="line">- (<span class="keyword">void</span>)xxx_viewDidAppear:(<span class="built_in">BOOL</span>)animated {</span>
 <span class="line">[<span class="keyword">self</span> xxx_viewDidAppear:animated];</span>
 <span class="line"><span class="built_in">NSLog</span>(<span class="string">@"viewDidAppear: %@"</span>, <span class="keyword">self</span>);</span>
<span class="line">}</span>
<span class="line"></span>
<span class="line"><span class="keyword">@end</span></span>
</pre>

 

</figure>

现在，当任何 UIViewController 对象或者 它的子类对象调用 `viewDidAppear` 方法时，都会打印出一个 log。

因为 Method Swizzling 是影响全局状态的，所以尽可能减少竞态条件的可能性非常重要。

`+ load` 方法保证在类初始化的时候执行，相比之下，`+ initialize` 不保证什么时候它会被执行（事实上，除非 app 直接对一个类发送消息，否则这个类的 `+ initialize` 方法不会被调用）。

# 0x07 动态创建一个类（Dynamically Creating a Class）

结合之前提到的动态属性和方法定义，`libobjc` 的终极大招是运行时创建一个类。

创建一个 `Product` 类，有两个属性：`name` 和 `price`：

<figure class="highlight objectivec">

 



<pre><span class="line"><span class="class"><span class="keyword">@interface</span> <span class="title">Product</span> : <span class="title">NSObject</span></span></span>
<span class="line"></span>
<span class="line"><span class="keyword">@property</span> (<span class="keyword">readonly</span>) <span class="built_in">NSString</span> *name;</span>
<span class="line"><span class="keyword">@property</span> (<span class="keyword">readonly</span>) <span class="keyword">double</span> price;</span>
<span class="line"></span>
<span class="line">- (instancetype)initWithName:(<span class="built_in">NSString</span> *)name price:(<span class="keyword">double</span>)price;</span>
<span class="line"></span>
<span class="line"><span class="keyword">@end</span></span>
<span class="line"></span>
<span class="line"><span class="class"><span class="keyword">@implementation</span> <span class="title">Product</span></span></span>
<span class="line"></span>
<span class="line">- (instancetype)initWithName:(<span class="built_in">NSString</span> *)name price:(<span class="keyword">double</span>)price {</span>
 <span class="line"><span class="keyword">if</span> (<span class="keyword">self</span> = [<span class="keyword">super</span> init]) {</span>
 <span class="line"><span class="keyword">self</span><span class="variable">.name</span> = name;</span>
 <span class="line"><span class="keyword">self</span><span class="variable">.price</span> = price;</span>
 <span class="line">}</span>
 <span class="line"></span> 
 <span class="line"><span class="keyword">return</span> <span class="keyword">self</span>;</span>
<span class="line">}</span>
</pre>

 

</figure>

运行时创建：

<figure class="highlight objectivec">
<pre><span class="line">Class c = objc_allocateClassPair([<span class="built_in">NSObject</span> class], <span class="string">"Product"</span>, <span class="number">0</span>);</span>
<span class="line">class_addIvar(c, <span class="string">"name"</span>, <span class="keyword">sizeof</span>(<span class="keyword">id</span>), log2(<span class="keyword">sizeof</span>(<span class="keyword">id</span>)), @encode(<span class="keyword">id</span>));</span>
<span class="line">class_addIvar(c, <span class="string">"price"</span>, <span class="keyword">sizeof</span>(<span class="keyword">double</span>), log2(<span class="keyword">sizeof</span>(<span class="keyword">double</span>)), @encode(<span class="keyword">double</span>));</span>
<span class="line"></span>
<span class="line">Ivar nameIvar = class_getInstanceVariable(c, <span class="string">"name"</span>);</span>
<span class="line">ptrdiff_t priceIvarOffset = ivar_getOffset(class_getInstanceVariable(c, <span class="string">"price"</span>));</span>
<span class="line"></span>
<span class="line">IMP initIMP = imp_implementationWithBlock(^(<span class="keyword">id</span> <span class="keyword">self</span>, <span class="built_in">NSString</span> *name, <span class="keyword">double</span> price) {</span>
 <span class="line">object_setIvar(<span class="keyword">self</span>, nameIvar, name);</span>
 <span class="line"></span> 
 <span class="line"><span class="keyword">char</span> *ptr = ((<span class="keyword">char</span> *)(__bridge <span class="keyword">void</span> *)<span class="keyword">self</span>) + priceIvarOffset;</span>
 <span class="line">memcpy(ptr, &price, <span class="keyword">sizeof</span>(price));</span>
 <span class="line"></span> 
 <span class="line"><span class="keyword">return</span> <span class="keyword">self</span>;</span>
<span class="line">});</span>
<span class="line"></span>
<span class="line"><span class="keyword">const</span> <span class="keyword">char</span> *initTypes = [[<span class="built_in">NSString</span> stringWithFormat:<span class="string">@"%s%s%s%s%s%s"</span>, @encode(<span class="keyword">id</span>), @encode(<span class="keyword">id</span>), @encode(SEL), @encode(<span class="keyword">id</span>), @encode(<span class="keyword">id</span>), @encode(<span class="built_in">NSUInteger</span>)] UTF8String];</span>
<span class="line">class_addMethod(c, <span class="keyword">@selector</span>(initWithFirstName:lastName:age), initIMP, initTypes);</span>
<span class="line"></span>
<span class="line">IMP nameIMP = imp_implementationWithBlock(^(<span class="keyword">id</span> <span class="keyword">self</span>) {</span>
 <span class="line"><span class="keyword">return</span> object_getIvar(<span class="keyword">self</span>, nameIvar);</span>
<span class="line">});</span>
<span class="line"><span class="keyword">const</span> <span class="keyword">char</span> *nameTypes = [[<span class="built_in">NSString</span> stringWithFormat:<span class="string">@"%s%s%s"</span>, @encode(<span class="keyword">id</span>), @encode(<span class="keyword">id</span>), @encode(SEL)] UTF8String];</span>
<span class="line">class_addMethod(c, <span class="keyword">@selector</span>(name), nameIMP, nameTypes);</span>
<span class="line"></span>
<span class="line">IMP priceIMP = imp_implementationWithBlock(^(<span class="keyword">id</span> <span class="keyword">self</span>) {</span>
 <span class="line"><span class="keyword">char</span> *ptr = ((<span class="keyword">char</span> *)(__bridge <span class="keyword">void</span> *)<span class="keyword">self</span>) + priceIvarOffset;</span>
 <span class="line"><span class="keyword">double</span> price;</span>
 <span class="line">memcpy(&price, ptr, <span class="keyword">sizeof</span>(price));</span>
 <span class="line"></span> 
 <span class="line"><span class="keyword">return</span> price;</span>
<span class="line">});</span>
<span class="line"><span class="keyword">const</span> <span class="keyword">char</span> *priceTypes = [[<span class="built_in">NSString</span> stringWithFormat:<span class="string">@"%s%s%s"</span>, @encode(<span class="keyword">double</span>), @encode(<span class="keyword">id</span>), @encode(SEL)] UTF8String];</span>
<span class="line">class_addMethod(c, <span class="keyword">@selector</span>(age), priceIMP, priceTypes);</span>
<span class="line"></span>
<span class="line">objc_registerClassPair(c);</span>
</pre>

 

</figure>

这里代码很多，我们来一点一点说。

首先，一个类用 `objc_allocateClassPair` 被分配了内存空间，指定了这个类的父类和类名。

然后，使用 `class_addIvar` 给这个类添加了实例变量。第四个参数用于确定变量的最小对齐方式（variable’s minimum alignment），这取决于 ivar 的类型和目标平台架构。

接下来是用 `imp_implementationWithBlock:` 定义初始化函数（initializer）的实现。调用 `object_setIvar` 设置 `name`。`price` 是通过之前计算的 offset 执行 `memcpy` 来设置的。

为了添加初始化函数，需要计算每个参数的类型编码。`@encode` 和 字符串插值有点尴尬混乱，但是就是这样的。

添加 ivar 的 getter 方法类似。

最后，一旦所有的方法都被添加，类就被注册到运行时。从那时起，`Product` 就可以像其他任何 Objective-C 类一样交互了：

<figure class="highlight objectivec">

 

<pre><span class="line">Product *widget = [[Product alloc] initWithName:<span class="string">@"Widget"</span> price:<span class="number">50.00</span>];</span>
<span class="line"><span class="built_in">NSLog</span>(<span class="string">@"%@: %g"</span>, widget<span class="variable">.name</span>, widget,price);</span>
</pre>

 

</figure>

</div>

<footer class="post-footer">


