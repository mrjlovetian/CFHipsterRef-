<script>(function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){ (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o), m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m) })(window,document,'script','https://www.google-analytics.com/analytics.js','ga'); ga('create', 'UA-69209675-1', 'auto'); ga('send', 'pageview');</script>

<div class="container sidebar-position-left page-post-detail ">

<header id="header" class="header" itemscope="" itemtype="http://schema.org/WPHeader">

<div class="header-inner">

<div class="site-brand-wrapper">

<div class="site-meta ">

<div class="custom-logo-site-title">[<span class="logo-line-before"></span><span class="site-title">Happy Hacking</span><span class="logo-line-after"></span>](/)</div>

我的眼里只有代码

</div>

<div class="site-nav-toggle"><button><span class="btn-bar"></span><span class="btn-bar"></span><span class="btn-bar"></span></button></div>

</div>

<nav class="site-nav">

*   [
    首页](/)
*   [
    归档](/archives)
*   [
    分类](/categories)
*   [
    标签](/tags)
*   [
    关于](/about)
*   [
    留言](/guestbook)

</nav>

</div>

</header>

<main id="main" class="main">

<div class="main-inner">

<div class="content-wrap">

<div id="content" class="content">

<div id="posts" class="posts-expand">

<article class="post post-type-normal " itemscope="" itemtype="http://schema.org/Article"><link itemprop="mainEntityOfPage" href="http://meilbn.com/2016/10/27/CFHipsterRef-Low-Level-Programming-on-iOS-OSX-Chapter2-Objective-C-Runtime/"> <span hidden="" itemprop="author" itemscope="" itemtype="http://schema.org/Person"> <meta itemprop="name" content="我的眼里只有代码"> <meta itemprop="description" content=""> <meta itemprop="image" content="/images/default_avatar.jpg"> </span> <span hidden="" itemprop="publisher" itemscope="" itemtype="http://schema.org/Organization"><meta itemprop="name" content="Happy Hacking"></span> 

<header class="post-header">

# Objective-C Runtime

<div class="post-meta"><span class="post-time"><span class="post-meta-item-icon"></span><span class="post-meta-item-text">发表于</span> <time title="创建于" itemprop="dateCreated datePublished" datetime="2016-10-27T17:09:06+08:00">2016-10-27</time> </span><span class="post-category"><span class="post-meta-divider">|</span> <span class="post-meta-item-icon"></span><span class="post-meta-item-text">分类于</span> <span itemprop="about" itemscope="" itemtype="http://schema.org/Thing">[<span itemprop="name">学习笔记</span>](/categories/学习笔记/) </span></span><span class="post-comments-count"><span class="post-meta-divider">|</span> <span class="post-meta-item-icon"></span>[<span class="post-comments-count disqus-comment-count" data-disqus-identifier="2016/10/27/CFHipsterRef-Low-Level-Programming-on-iOS-OSX-Chapter2-Objective-C-Runtime/" itemprop="commentCount"></span>](/2016/10/27/CFHipsterRef-Low-Level-Programming-on-iOS-OSX-Chapter2-Objective-C-Runtime/#comments)</span><span id="/2016/10/27/CFHipsterRef-Low-Level-Programming-on-iOS-OSX-Chapter2-Objective-C-Runtime/" class="leancloud_visitors" data-flag-title="Objective-C Runtime"><span class="post-meta-divider">|</span> <span class="post-meta-item-icon"></span><span class="post-meta-item-text">阅读次数</span><span class="leancloud-visitors-count"></span></span></div>

</header>

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

<figure class="highlight objectivec">

| 

<pre><span class="line">1</span>
</pre>

 | 

<pre><span class="line">[object message];</span>
</pre>

 |

</figure>

然后编译器最后会将该语句翻译成：

<figure class="highlight objectivec">

| 

<pre><span class="line">1</span>
</pre>

 | 

<pre><span class="line">objc_msgSend(object, <span class="keyword">@selector</span>(message));</span>
</pre>

 |

</figure>

`objc_msgSend` 有几种形式：

*   `objc_msgSend`：向一个类发送简单返回值类型的消息
*   `objc_msgSend_stret`：向一个类发送返回值类型为结构体的消息
*   `objc_msgSendSuper`：向父类发送简单返回值类型的消息
*   `objc_msgSendSuper_stert`：向父类发送返回值类型为结构体的消息

# 0x03 属性元编程（Metaprogramming with Properties）

属性定义了对象状态的公共接口。

例如，访问一个对象的属性列表，我们可以通过属性元编程来避免手动实现 `NSCoding`：

<figure class="highlight objectivec">

| 

<pre><span class="line">1</span>
<span class="line">2</span>
<span class="line">3</span>
<span class="line">4</span>
<span class="line">5</span>
<span class="line">6</span>
<span class="line">7</span>
<span class="line">8</span>
<span class="line">9</span>
<span class="line">10</span>
<span class="line">11</span>
<span class="line">12</span>
<span class="line">13</span>
<span class="line">14</span>
<span class="line">15</span>
<span class="line">16</span>
<span class="line">17</span>
<span class="line">18</span>
<span class="line">19</span>
<span class="line">20</span>
<span class="line">21</span>
<span class="line">22</span>
<span class="line">23</span>
<span class="line">24</span>
<span class="line">25</span>
<span class="line">26</span>
<span class="line">27</span>
<span class="line">28</span>
<span class="line">29</span>
</pre>

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

 |

</figure>

# 0x04 关联对象（Associated Objects）

关联对象是 Objective-C 运行时的一个特性，允许开发者对已经存在的类在扩展中添加自定义的属性，这非常有用。

关联对象使用非常简单，用 `objc_setAssociatedObject` 存储一个关联值，用 `objc_getAssociatedObject` 获取这个关联值。

任何关联对象都是通过一个 key 来关联的，这个 key 可以是任何常量值。最简单的方法就是传入 getter 方法选择器（方法选择器在运行时保证是唯一且不变的）。

<figure class="highlight objectivec">

| 

<pre><span class="line">1</span>
<span class="line">2</span>
<span class="line">3</span>
<span class="line">4</span>
<span class="line">5</span>
<span class="line">6</span>
<span class="line">7</span>
<span class="line">8</span>
<span class="line">9</span>
<span class="line">10</span>
<span class="line">11</span>
<span class="line">12</span>
<span class="line">13</span>
<span class="line">14</span>
<span class="line">15</span>
</pre>

 | 

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

 |

</figure>

# 0x05 动态添加方法（Dynamically Adding a Method）

正如属性描述了一个对象的状态，方法则构成了对象的行为。

在 Objective-C 中，方法以 `+` 或 `-` 前置声明，表示该方法是类方法还是实例方法（也就是说是通过类调用的还是类的对象来调用的）。

一个类可以通过创建一个 category 来添加新的方法，然后可以在包含了这个 category 的地方使用这些新的方法。

不过，方法也可以在运行时创建：

<figure class="highlight objectivec">

| 

<pre><span class="line">1</span>
<span class="line">2</span>
<span class="line">3</span>
<span class="line">4</span>
<span class="line">5</span>
<span class="line">6</span>
<span class="line">7</span>
</pre>

 | 

<pre><span class="line">Class c = [<span class="built_in">NSObject</span> class];</span>
<span class="line">IMP addingMethodIMP = imp_implementationWithBlock((<span class="built_in">NSString</span> *)^(<span class="keyword">id</span> <span class="keyword">self</span>, <span class="keyword">id</span> arg1) {</span>
 <span class="line"><span class="keyword">return</span> [<span class="built_in">NSString</span> stringWithFormat:<span class="string">@"arg1 = %@"</span>, arg1];</span>
<span class="line">});</span>
<span class="line"></span>
<span class="line"><span class="keyword">const</span> <span class="keyword">char</span> *addingMethodTypes = [[<span class="built_in">NSString</span> stringWithFormat:<span class="string">@"%s%s%s"</span>, @encode(<span class="keyword">id</span>), @encode(<span class="keyword">id</span>), @encode(SEL)] UTF8String];</span>
<span class="line">class_addMethod(c, <span class="keyword">@selector</span>(addingMethodWithArg:), addingMethodIMP, addingMethodTypes);</span>
</pre>

 |

</figure>

# 0x06 Method Swizzling

Method Swizzling 是改变一个已存在的方法的实现的过程。这个技术是改变一个映射到一个类的调度表中的底层函数的方法，在运行时修改为调用自己定义的方法。

比如在某些时候想要跟踪一个应用里面的每个 ViewController 在一次生命周期内打开的次数。想要实现这个功能有几种方法：

1.  每个 ViewController 都添加跟踪代码到重载的 `viewDidAppear` 方法里面，但是这样会有很多重复的代码。
2.  子类化 UIViewController，但是这样的话，还要子类化 `UITableViewController`、`UINavigationController` 等其他用到的 ViewController，还是要添加很多重复的代码。

有了 Method Swizzling，解决办法就非常优雅了：

<figure class="highlight objectivec">

| 

<pre><span class="line">1</span>
<span class="line">2</span>
<span class="line">3</span>
<span class="line">4</span>
<span class="line">5</span>
<span class="line">6</span>
<span class="line">7</span>
<span class="line">8</span>
<span class="line">9</span>
<span class="line">10</span>
<span class="line">11</span>
<span class="line">12</span>
<span class="line">13</span>
<span class="line">14</span>
<span class="line">15</span>
<span class="line">16</span>
<span class="line">17</span>
<span class="line">18</span>
<span class="line">19</span>
<span class="line">20</span>
<span class="line">21</span>
<span class="line">22</span>
<span class="line">23</span>
<span class="line">24</span>
<span class="line">25</span>
<span class="line">26</span>
<span class="line">27</span>
<span class="line">28</span>
<span class="line">29</span>
<span class="line">30</span>
<span class="line">31</span>
<span class="line">32</span>
</pre>

 | 

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

 |

</figure>

现在，当任何 UIViewController 对象或者 它的子类对象调用 `viewDidAppear` 方法时，都会打印出一个 log。

因为 Method Swizzling 是影响全局状态的，所以尽可能减少竞态条件的可能性非常重要。

`+ load` 方法保证在类初始化的时候执行，相比之下，`+ initialize` 不保证什么时候它会被执行（事实上，除非 app 直接对一个类发送消息，否则这个类的 `+ initialize` 方法不会被调用）。

# 0x07 动态创建一个类（Dynamically Creating a Class）

结合之前提到的动态属性和方法定义，`libobjc` 的终极大招是运行时创建一个类。

创建一个 `Product` 类，有两个属性：`name` 和 `price`：

<figure class="highlight objectivec">

| 

<pre><span class="line">1</span>
<span class="line">2</span>
<span class="line">3</span>
<span class="line">4</span>
<span class="line">5</span>
<span class="line">6</span>
<span class="line">7</span>
<span class="line">8</span>
<span class="line">9</span>
<span class="line">10</span>
<span class="line">11</span>
<span class="line">12</span>
<span class="line">13</span>
<span class="line">14</span>
<span class="line">15</span>
<span class="line">16</span>
<span class="line">17</span>
<span class="line">18</span>
<span class="line">19</span>
</pre>

 | 

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

 |

</figure>

运行时创建：

<figure class="highlight objectivec">

| 

<pre><span class="line">1</span>
<span class="line">2</span>
<span class="line">3</span>
<span class="line">4</span>
<span class="line">5</span>
<span class="line">6</span>
<span class="line">7</span>
<span class="line">8</span>
<span class="line">9</span>
<span class="line">10</span>
<span class="line">11</span>
<span class="line">12</span>
<span class="line">13</span>
<span class="line">14</span>
<span class="line">15</span>
<span class="line">16</span>
<span class="line">17</span>
<span class="line">18</span>
<span class="line">19</span>
<span class="line">20</span>
<span class="line">21</span>
<span class="line">22</span>
<span class="line">23</span>
<span class="line">24</span>
<span class="line">25</span>
<span class="line">26</span>
<span class="line">27</span>
<span class="line">28</span>
<span class="line">29</span>
<span class="line">30</span>
<span class="line">31</span>
<span class="line">32</span>
<span class="line">33</span>
<span class="line">34</span>
<span class="line">35</span>
<span class="line">36</span>
</pre>

 | 

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

 |

</figure>

这里代码很多，我们来一点一点说。

首先，一个类用 `objc_allocateClassPair` 被分配了内存空间，指定了这个类的父类和类名。

然后，使用 `class_addIvar` 给这个类添加了实例变量。第四个参数用于确定变量的最小对齐方式（variable’s minimum alignment），这取决于 ivar 的类型和目标平台架构。

接下来是用 `imp_implementationWithBlock:` 定义初始化函数（initializer）的实现。调用 `object_setIvar` 设置 `name`。`price` 是通过之前计算的 offset 执行 `memcpy` 来设置的。

为了添加初始化函数，需要计算每个参数的类型编码。`@encode` 和 字符串插值有点尴尬混乱，但是就是这样的。

添加 ivar 的 getter 方法类似。

最后，一旦所有的方法都被添加，类就被注册到运行时。从那时起，`Product` 就可以像其他任何 Objective-C 类一样交互了：

<figure class="highlight objectivec">

| 

<pre><span class="line">1</span>
<span class="line">2</span>
</pre>

 | 

<pre><span class="line">Product *widget = [[Product alloc] initWithName:<span class="string">@"Widget"</span> price:<span class="number">50.00</span>];</span>
<span class="line"><span class="built_in">NSLog</span>(<span class="string">@"%@: %g"</span>, widget<span class="variable">.name</span>, widget,price);</span>
</pre>

 |

</figure>

</div>

<footer class="post-footer">

<div class="post-tags">[# Objective-C](/tags/Objective-C/) [# Runtime](/tags/Runtime/)</div>

<div class="post-nav">

<div class="post-nav-next post-nav-item">[使用过的第三方服务](/2016/10/05/used-third-party-services/ "使用过的第三方服务")</div>

<span class="post-nav-divider"></span>

<div class="post-nav-prev post-nav-item">[Grand Central Dispatch](/2016/10/29/CFHipsterRef-Low-Level-Programming-on-iOS-OSX-Chapter5-Grand-Central-Dispatch/ "Grand Central Dispatch")</div>

</div>

</footer>

</article>

</div>

</div>

<div class="comments" id="comments">

<div id="disqus_thread">

<noscript>Please enable JavaScript to view the [comments powered by Disqus.](//disqus.com/?ref_noscript)</noscript>

</div>

</div>

</div>

<div class="sidebar-toggle">

<div class="sidebar-toggle-line-wrap"><span class="sidebar-toggle-line sidebar-toggle-line-first"></span><span class="sidebar-toggle-line sidebar-toggle-line-middle"></span><span class="sidebar-toggle-line sidebar-toggle-line-last"></span></div>

</div>

<aside id="sidebar" class="sidebar">

<div class="sidebar-inner">

*   文章目录
*   站点概览

<section class="site-overview sidebar-panel">

<div class="site-author motion-element" itemprop="author" itemscope="" itemtype="http://schema.org/Person">![我的眼里只有代码](/images/default_avatar.jpg)

我的眼里只有代码

You are the code of my eye.
'Cause you are the apple of my eye.

</div>

<nav class="site-state motion-element">

<div class="site-state-item site-state-posts">[<span class="site-state-item-count">33</span> <span class="site-state-item-name">日志</span>](/archives)</div>

<div class="site-state-item site-state-categories">[<span class="site-state-item-count">8</span> <span class="site-state-item-name">分类</span>](/categories/index.html)</div>

<div class="site-state-item site-state-tags">[<span class="site-state-item-count">52</span> <span class="site-state-item-name">标签</span>](/tags/index.html)</div>

</nav>

<div class="links-of-author motion-element"><span class="links-of-author-item">[GitHub](https://github.com/meilbn "GitHub") </span><span class="links-of-author-item">[Weibo](http://weibo.com/fuckingcode "Weibo") </span><span class="links-of-author-item">[Twitter](https://twitter.com/meilbn "Twitter") </span><span class="links-of-author-item">[Facebook](https://www.facebook.com/meilbn "Facebook") </span><span class="links-of-author-item">[Instagram](https://instagram.com/meilbn/ "Instagram") </span><span class="links-of-author-item">[GooglePlus](https://plus.google.com/u/0/106344002269408415978/posts "GooglePlus") </span><span class="links-of-author-item">[ZhiHu](http://www.zhihu.com/people/meilbn/ "ZhiHu") </span><span class="links-of-author-item">[DouBan](http://www.douban.com/people/codemaker/ "DouBan")</span></div>

<div class="cc-license motion-element" itemprop="license">[![Creative Commons](/images/cc-by-nc-sa.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/) </div>

</section>

<section class="post-toc-wrap motion-element sidebar-panel sidebar-panel-active">

<div class="post-toc">

<div class="post-toc-content">

1.  [<span class="nav-number">1.</span> <span class="nav-text">0x00 前言</span>](#0x00_前言)
2.  [<span class="nav-number">2.</span> <span class="nav-text">0x01 libobjc</span>](#0x01_libobjc)
3.  [<span class="nav-number">3.</span> <span class="nav-text">0x02 发送消息（Message Sending）</span>](#0x02_发送消息（Message_Sending）)
4.  [<span class="nav-number">4.</span> <span class="nav-text">0x03 属性元编程（Metaprogramming with Properties）</span>](#0x03_属性元编程（Metaprogramming_with_Properties）)
5.  [<span class="nav-number">5.</span> <span class="nav-text">0x04 关联对象（Associated Objects）</span>](#0x04_关联对象（Associated_Objects）)
6.  [<span class="nav-number">6.</span> <span class="nav-text">0x05 动态添加方法（Dynamically Adding a Method）</span>](#0x05_动态添加方法（Dynamically_Adding_a_Method）)
7.  [<span class="nav-number">7.</span> <span class="nav-text">0x06 Method Swizzling</span>](#0x06_Method_Swizzling)
8.  [<span class="nav-number">8.</span> <span class="nav-text">0x07 动态创建一个类（Dynamically Creating a Class）</span>](#0x07_动态创建一个类（Dynamically_Creating_a_Class）)

</div>

</div>

</section>

</div>

</aside>

</div>

</main>

<footer id="footer" class="footer">

<div class="footer-inner"><link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/font-awesome/4.3.0/css/font-awesome.min.css">

<div class="copyright">© 2015 - <span itemprop="copyrightYear">2017</span> <span class="with-love"></span><span class="author" itemprop="copyrightHolder">我的眼里只有代码</span></div>

<div class="theme-info"><span id="busuanzi_container_site_pv">本站总访问量 <span id="busuanzi_value_site_pv"></span>次，</span> <span id="busuanzi_container_site_uv">访客数 <span id="busuanzi_value_site_uv"></span>人次</span></div>

</div>

</footer>

</div>

<script type="text/javascript">if (Object.prototype.toString.call(window.Promise) !== '[object Function]') { window.Promise = null; }</script> <script type="text/javascript">var disqus_shortname = 'ihappyhacking'; var disqus_identifier = '2016/10/27/CFHipsterRef-Low-Level-Programming-on-iOS-OSX-Chapter2-Objective-C-Runtime/'; var disqus_title = "Objective-C Runtime"; function run_disqus_script(disqus_script) { var dsq = document.createElement('script'); dsq.type = 'text/javascript'; dsq.async = true; dsq.src = '//' + disqus_shortname + '.disqus.com/' + disqus_script; (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(dsq); } run_disqus_script('count.js'); var disqus_config = function () { this.page.url = disqus_url; this.page.identifier = disqus_identifier; this.page.title = disqus_title; }; run_disqus_script('embed.js');</script> <script>AV.initialize("dFo2NvCcL1s5zeJtuvzonGrj-gzGzoHsz", "3gyJUovHABTEqJVyX08exA3z");</script> <script>function showTime(Counter) { var query = new AV.Query(Counter); var entries = []; var $visitors = $(".leancloud_visitors"); $visitors.each(function () { entries.push( $(this).attr("id").trim() ); }); query.containedIn('url', entries); query.find() .done(function (results) { var COUNT_CONTAINER_REF = '.leancloud-visitors-count'; if (results.length === 0) { $visitors.find(COUNT_CONTAINER_REF).text(0); return; } for (var i = 0; i < results.length; i++) { var item = results[i]; var url = item.get('url'); var time = item.get('time'); var element = document.getElementById(url); $(element).find(COUNT_CONTAINER_REF).text(time); } for(var i = 0; i < entries.length; i++) { var url = entries[i]; var element = document.getElementById(url); var countSpan = $(element).find(COUNT_CONTAINER_REF); if( countSpan.text() == '') { countSpan.text(0); } } }) .fail(function (object, error) { console.log("Error: " + error.code + " " + error.message); }); } function addCount(Counter) { var $visitors = $(".leancloud_visitors"); var url = $visitors.attr('id').trim(); var title = $visitors.attr('data-flag-title').trim(); var query = new AV.Query(Counter); query.equalTo("url", url); query.find({ success: function(results) { if (results.length > 0) { var counter = results[0]; counter.fetchWhenSave(true); counter.increment("time"); counter.save(null, { success: function(counter) { var $element = $(document.getElementById(url)); $element.find('.leancloud-visitors-count').text(counter.get('time')); }, error: function(counter, error) { console.log('Failed to save Visitor num, with error message: ' + error.message); } }); } else { var newcounter = new Counter(); /* Set ACL */ var acl = new AV.ACL(); acl.setPublicReadAccess(true); acl.setPublicWriteAccess(true); newcounter.setACL(acl); /* End Set ACL */ newcounter.set("title", title); newcounter.set("url", url); newcounter.set("time", 1); newcounter.save(null, { success: function(newcounter) { var $element = $(document.getElementById(url)); $element.find('.leancloud-visitors-count').text(newcounter.get('time')); }, error: function(newcounter, error) { console.log('Failed to create'); } }); } }, error: function(error) { console.log('Error:' + error.code + " " + error.message); } }); } $(function() { var Counter = AV.Object.extend("Counter"); if ($('.leancloud_visitors').length == 1) { addCount(Counter); } else if ($('.post-title-link').length > 1) { showTime(Counter); } });</script>

