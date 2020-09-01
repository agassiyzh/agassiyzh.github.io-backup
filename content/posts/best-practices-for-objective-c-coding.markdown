---
title: "Best Practices for Objective-C Coding"
date: 2012-04-29T17:22:00+08:00
categories: [objectiv-C]
---
**在[v2ex][v2ex]有朋友推荐了这篇文章。做了翻译。欢迎纠错和讨论**

---

## 前言

我通常不会在我的blog中写和技术非常相关的东西，但是我希望对Mac和iOS(iPhone & iPad)的开发社区做点贡献。所以，如果你不是apple开发者，自行绕道。

## 介绍

这篇文章积累了一些多年使用objc这门语言的一些程序员公认为有效的最佳实践。我之所以把它们称作为“戒律”，是因为有很多理由可以证明它们。但是当我把这些实践拿给别的开发者看，他们往往会非常反对。。。

<!--more-->

## 强烈反对：这不会影响性能吗？

这永远不会是一个合理的反对理由。如果你可以给我指出，任何一个下面列出的方法中造成了性能瓶颈。只要你在代码中清除注释了如何打破这些策略，你可以得到我的许可在这些原则之外优化一下代码。

### 为什么？

下面这些策略的目的是为了让代码安全，节约内存，容易被理解，和容易维护。他们大多数不是为了增强性能。尽管如此，写出的高效的代码**不是**我们首先要考虑的事情。一般情况下，我们要写干净，正确的代码，只有在分析后表明你需要优化的时候再去作优化。在一个典型的靠网络接入驱动UI的应用程序中，最大的瓶颈往往是用户操作，接下来是网络速率，然后是磁盘存取效率。然而，那些过早作优化的程序员总是担心如果他们不用尽方法对他们程序进行优化，那么他们的程序会慢得象狗一样。这肯定是不对的。

## 重要的建议

即使你在写的时候很清楚你的代码是怎么运作的，那也不是就意味着别人去修改你的代码的时候就能够完全明白，也不能保证在你几个月以后去维护的时候你自己还是会明白。**总是**要努力使你代码中的变量能自文档化，并且在代码本身不是很能够说明的时候加上详细的注释。每当你需要解决一个bug或者使用一个功能不完整的API，把你的解决操作封装在一个方法中，并在你的注释中写上类似[“KLUDGE”][KLUDGE]，那样的话以后可以很容易找到并且修复这些难点。

-------
汝等…

**尽可能**使用[Automatic Reference Counting][ARC]。在这篇文章中，已经把ARC之前形式的代码移除了。所有的代码用ARC的方式写了。

-------
汝等…

**总是**在申明实例变量之前用@private标注，并且**一定不要**在类外部存取实例变量。

为什么？

- 保护隐藏信息（封装）
- 限制类的实现方法直接访问实例变量。
- 实例变量的默认权限是@protected，这意味着子类可以自由的访问这些实例变量。但是，必须有一个很好的理由允许子类这么做——父类暴露给外部世界的所有东西都成了它契约的一部分，改变他的API或者合约是面向对象封装非常重要的一个好处。通过让你的实例变量保持为私有，你可以让你的类变的很清楚。他们就是具体实现而不是类的一部分。

这样不好：

```objc
@interface Foo : NSObject {
        int a;
        NSObject* b;
        // …
}
// method & property declarations…
@end
```

这样比较好：

```objc
@interface Foo : NSObject {
        @private
        int a;
        NSObject* b;
        // …
}
// method & property declarations…
@end
```

---------

汝等…

**总是要**为所有成员数据创建@property，并且用“self.name”来访问它通过类实现。**千万不要**访问你的实例变量。

为什么？

- Proberties强制指定访问权限。
- Proberties强制指定内存管理方式。
- Proberties提供了自定义getter和setter。
- Proberties中自定义的getter和setter方法可以强制线程安全。
- 有一个唯一的途径访问变量，增加代码可读性。

这样不好：

```objc
@interface Foo : NSObject {
        @private
        NSObject* myObj;
}
@end
 
@implementation Foo
- (void)bar {
        myObj = nil;
}
@end
```

这样比较好：

```objc
@interface Foo : NSObject {
        @private
        NSObject* myObj;
}
 
@property(strong, nonatomic) NSObject* myObj;
 
@end
 
@implementation Foo
- (void)bar {
        self.myObj = nil;
}
@end
```

-------------

汝等…

**总是**在你的Proberties中使用“nonatomic”属性，除非你写了一个线程安全的类，并且确实需要访问atomic，那么你应该**在你的代码中把你的意图写在注释中**。

为什么？

类属性没有被定义为“nonatomic”也许会给人在头脑中的印象是他被设计为线程安全的，然而事实上他们不是这样的。只有当你真正需要线程安全的时候才把“nonautomic”从properties中去掉。

这样不好：

```objc
@interface Foo : NSObject
 
@property(strong) NSObject* myObj;
 
@end
```

这样比较好：

```objc
@interface Foo : NSObject
 
@property(strong, nonatomic) NSObject* myObj;
 
@end
```

这样比较好：

```objc
// 这个类和它的所有属性都是线程安全的。
@interface Foo : NSObject
 
@property(strong) NSObject* myObj;
 
@end
```

-----

汝等…

**不要**让你的实例变量名和属性名造成混淆，或者和成员变量名混淆。**总是**让你自己的实例变量名用下划线结尾。**除非**你的类继承自一个第三方的类，那个类中已经有了和成员变量同名的实例变量。在那种情况下选择另一个不用的名字，或者加另一条下划线**并注释为什么要这么做**。

在implementation中用```@synthesize name = name_;```代替```@synthesize name;```

为什么？

- 在类的实现中，你总是会不得不访问成员变量替代property存取器。
- Apple把```"_name"```作为他们私有的实例变量。把```"name_"```作为你自己的变量可以避免混淆。
- Apple已经开始在他们的的示例代码中使用```"name_"```了。

这样不好：

```objc
@interface Foo : NSObject
 
@property(strong, nonatomic) NSObject* myObj;
 
@end
 
// …
 
@implementation Foo
 
@synthesize myObj;
 
@end
```

这样好点：

```objc

@interface Foo : NSObject
 
@property(strong, nonatomic) NSObject* myObj;
 
@end
 
// …
 
@implementation Foo
 
@synthesize myObj = myObj_;
 
@end

```

-----

汝等…

**千万不要**多于得在@instance中添加成员变量。用@synthesize标志默默得添加这些成员变量好了。

下面得这个实践在很多类里面都会出现，在@interface中很明确的没有定义实例变量。当一个class没有实例变量得时候，可以忽略@private的定义，甚至可以忽略成员变量的括号。

为什么呢？

- 减少冗余。
- 简化类的头。
- 为了避免类之前的定义需要，你可以把类的成员定已在使用之前。

这样不好：

```objc

@interface Foo : NSObject {
        @private
        NSObject* myObj_;
}
 
@property(strong, nonatomic) NSObject* myObj;
 
@end
 
// …
 
@implementation Foo
 
@synthesize myObj = myObj_;
 
@end

```

这样比较好：

```objc

@interface Foo : NSObject
 
@property(strong, nonatomic) NSObject* myObj;
 
@end
 
// …
 
@implementation Foo
 
@synthesize myObj = myObj_;
 
@end

```

当你在get，set方法中需要用到的时候你还是需要访问一下带有下划线的变量。

这样是无法工作的：

```objc

@interface Foo : NSObject
 
@property(strong, nonatomic) NSObject* myObj;
 
@end
 
// …
 
@implementation Foo
 
@synthesize myObj;
 
- (NSObject*)myObj
{
        return self.myObj; // recursive call to this getter!
}
 
- (void)setMyObj:(NSObject*)myObj
{
        self.myObj = myObj; // recursive call to this setter!
}
 
@end

```

这样就可以了：

```objc

@interface Foo : NSObject
 
@property(strong, nonatomic) NSObject* myObj;
 
@end
 
// …
 
@implementation Foo
 
@synthesize myObj = myObj_;
 
- (NSObject*)myObj
{
        return myObj_; // No problem.
}
 
- (void)setMyObj:(NSObject*)myObj
{
        // no problem
        myObj_ = myObj; // do the assignment (ARC handles any necessary retaining and releasing)
}
 
@end

```

--------

汝等…

**千万不要**通过带有下划线的变量访问成员数据，除非在get，set方法中。

这样不好：

```objc

@interface Foo : NSObject
 
@property(strong, nonatomic) Bar* myObj;
 
@end
 
// …
 
@implementation Foo
 
@synthesize myObj = myObj_;
 
- (void)someMethod
{
        myObj_ = [[Bar alloc] init];
}
 
@end

```

这样比较好:

```objc

@interface Foo : NSObject
 
@property(strong, nonatomic) Bar* myObj;
 
@end
 
// …
 
@implementation Foo
 
@synthesize myObj = myObj_;
 
- (void)someMethod
{
        self.myObj = [[Bar alloc] init];
}
 
@end

```

--------

汝等…

**千万不要**把私有方法或者私有的property定义在类的头文件中。**应该**把所有的私有方法或者property定义在实现文件的类扩展方法中。

这样不好:

```objc

//
// Foo.h
//
 
@interface Foo : NSObject
 
@property(nonatomic) int myPublicProperty;
@property(strong, nonatomic) Bar* myPrivateProperty; // This can be accessed by anyone who includes the header
 
- (int)myPublicMethod;
- (int)myPrivateMethod; // So can this.
 
@end

```

这样比较好：

```objc

//
// Foo.h
//
 
@interface Foo : NSObject
 
// Only the public API can be accessed by including the header
 
@property(nonatomic) int myPublicProperty;
 
- (int)myPublicMethod;
 
@end

//
// Foo.m
//
 
@interface Foo () // This is a "class extension" and everything declared in it is private, because it’s in the implementation file
 
@property(strong, nonatomic) Bar* myPrivateProperty;
 
- (int)myPrivateMethod;
 
@end
 
@implementation Foo
// …
@end

```

----------------

汝等…

**不要**在一个方法中不要写超过一个的```return```语句，只是在方法的最后一句返回一个空值。

在方法的一开始定义一个空的数据类型，然后在代码的不同路径中给他附值，在方法的最后把它return。**不要**过早把它return掉。

为什么？

过早的出现return语句，会造成一些资源的分配不备执行。

这样不好：

```objc

@implementation Foo
 
- (Bar*)barWithInt:(int)n
{
        // Allocate some resource here…
 
        if(n == 0) {
                // …and you have to deallocate the resource here…
                return [[Bar alloc] init];
        } else if(n == 1) {
                // …and here…
                return self.myBar;
        }
 
        // …and here.
        return nil;
}
 
@end

```

这样比较好：

```objc

@implementation Foo
 
- (Bar*)barWithInt:(int)n
{
        Bar* result = nil;
 
        // Allocate some resource here…
 
        if(n == 0) {
                result = [[Bar alloc] init];
        } else if(n == 1) {
                result = self.myBar;
        }
 
        // …and deallocate the resource here, you’re done!
 
        return result;
}
 
@end

```

------------

汝等…

要明白autorelease pools是用来干什么的，它们是什么时候对你来说创建和销毁的。

- 每次NSRunLoop运行的时候都会自动启一个
- 每个NSoperation都会自动起一个
- 对你来说，在一条线程的开始到结束会有一个autorelease pool
- 对你来说，当你需要大量创建和销毁一些对象的时候，你可以把控制权交给loop。

在用ARC的时候，你可以用@autoreloeasepool {…} 来创建。

-------

汝等…

**总是**优先使用类的便捷构造方法。所有foundation framework容器类都提供了这种构造方法。

这样不好：

```objc

NSMutableDictionary* dict = [[NSMutableDictionary alloc] init];

```

这样比较好：

```objc

NSMutableDictionary* dict = [NSMutableDictionary dictionary];

```

----------------

汝等…

**总是**应该在你写的类的API中提供加速构造方法。

为什么？

使用你写的类的用户会方便点。

这样不好:

```objc
@interface Foo : NSObject
 
- (id)initWithBar:(int)bar baz:(int)baz;
 
@end
```

这样好点：

```objc

@interface Foo : NSObject
 
- (id)initWithBar:(int)bar baz:(int)baz;
 
+ (Foo*)fooWithBar:(int)bar baz:(int)baz;
 
@end

```

-----

汝等…

**应该**了解对象的所有权是什么时候发生更改的。ARC在这方面帮你做了许多事情。但是你还是应该大致了解一下这后面发生了写什么事情。

接管一个对象的常用方法：

- 当你调用＋alloc方法创建一个类的时候这个新的对象是属于你的。
- 当你对一个实例发－copy或者－mutableCopy消息后，创建的新对象是属于你的。
- 当你对你个property用retain或者strong属性分配对象，你会成为这个对象的拥有者。

释放一个对象的常用方法：

- 给你个strong的property分配一个新的对象（或者nil）。
- 一个local的变量超出了他的区域。
- 一个对象的饮用持有对象被销毁了。

-----------

汝等…

**应该**知道你的property和实例变量的内存管理规则，尤其当时自定义getter和setter的时候。确保你的实例变量使用了正确是存储策略，得以使ARC可以正常执行。最容易的方法是：用@synthedize 然后重载get，set方法，来访问下面的实例变量，那么它们就可以和perproty有相同的存储策略。

```objc 

@interface bar
 
@property (strong, nonatomic) id foo;
 
@end
 
 
@implementation bar
 
@synthesize foo = foo_;
 
- (id)foo
{
        return foo_;
}
 
- (void)setFoo:(id)foo;
{
        foo_ = foo;  // Retained/released automatically by ARC because of (strong) attribute on @property above
        [self syncToNewFoo];  // The reason for our custom setter
}
 
@end

```

-----------------

汝等…

**不要**在你们的类中写－delloc方法，除非有些特殊的资源需要release（比如：关闭文件，释放malloc出来的内存，作废计时器）。ARC可以吧其他的事情做好的。

------

汝等…

**应该**给property的getter和setter同时自定义。

为什么？

Getter和Setter需要在内存管理、线程安全和另外一些方便的表现是对称的。你不应该指望系统生成的get或者set方法和你自己写的get，set方法是对称的。所以，如果你打算自己写一个get或者set方法，那么你应该写对应的另一个。

----

汝等…

**一定要**给property写一个自定义方法，然后在dealloc方法中给这个property附值成nil，如果当你release一个对象的时候有些事情必须要做（比如作废一个timer）。

这样不好：

```objc

@implementation Foo
 
@synthesize myTimer;
 
- (void)dealloc
{
        self.myTimer = nil; // Timer not invalidated, we could get called back if the timer fires after we’re dealloced!
}
 
@end

```

这样更好点：

```objc
@implementation Foo
 
@synthesize myTimer = myTimer_;
 
- (NSTimer*)myTimer
{
        return myTimer_;
}
 
- (void)setMyTimer:(NSTimer*)myTimer
{
        [myTimer_ invalidate];
        myTimer_ = myTimer;
}
 
- (void)dealloc
{
        self.myTimer = nil; // Timer guaranteed not to fire after we’re gone! Still necessary under ARC.
}
 
@end
```

为什么啊？

在－dealloc的时候，ARC只关心去释放那些strong或者retain的实例变量，但是他就只做了这些，而不会调用你自己调用的set方法。所以如果你自己写的set方法做了一些其他方面的事情（比如废弃一个timer），你还是要确认有没有执行这个set方法。

----

汝等…

在写一些构造方法的时候，**总是**应该使在［super init］前面执行的代码尽量少。

为什么？

一般来说，调用父类的构造方法又可以失败的，导致它的返回为nil。如果出现了那样的情况，那么在父类构造前面做的动作全部都白费了，可能还要去恢复那些操作，这样使很不值得的。

这样不好：

```objc
@implementation Foo
 
- (id)initWithBar:(Bar*)bar
{
        [bar someMethod];
        // other pre-initialization here
       
        if(self = [super init]) {
                // other initialization here
        } else {
        // oops! failed to initialize super class
        // undo anything we did above
}
 
        return self;
}
 
@end
```

这样更好点：

```objc
@implementation Foo
 
- (id)init
{
        if(self = [super init]) {
                // minimal initialization here
        }
 
        return self;
}
 
// Other methods that put a Foo into a usable state
 
@end
```

-------

汝等…

在写UIViewController并且没有使用nib文件的时候，**总是**应该在－loadView中按照先后创建并且设置你的view，而**不是**在－init中。你的－loadview实现是你分配view属性唯一的地方。

---

汝等…

**永远不要**自己去执行－loadView方法！当访问UIViewController的时候，view属性是被延迟加载的。在内存不足的时候它是有可能被清理掉的，所以**千万不要**假设UIViewController的View的声明周期和Controller是一样长的。


----

汝等…

**总是应该**只创建你需要的view一次，在必要的时候展示，隐藏，或者移动它。**一定不要**为了一些改变重复创建和销毁它们。

----

汝等…

**一定不要**在UIView中自己去调用－drawRect。应该调用-setNeedsDisplay.

---

汝等…

**总是应该**避免在代码中出现复合调用。局部变量是你的好朋友哦。

这样不好:

```objc
NSMutableDictionary* listDict = [[NSMutableDictionary alloc] initWithDictionary:[[NSUserDefaults standardUserDefaults] objectForKey:@"foo"]];
```

这样更好:

```objc
NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
NSDictionary* dataModelDict = [defaults objectForKey:@"foo"];
NSMutableDictionary* listDict = [NSMutableDictionary dictionaryWithDictionary:dataModelDict];
```

为什么？

- 代码自己说明（self-Documenting）
- 容易理解
- 在debug的时候更爽


-----

汝等…

**不要**用像-drawUI这样的方法名，当你不是真的绘制操作的时候。**总是**应该把方法名设置成-setupUI或者-restUI之类的。尤其是方法需要被多次调用的时候。

-----

汝等…

**千万不要**不要让自己做重复操作。没一个信息或者机制应该放在单一的权威的地方，并且通过程序去部署，甚至这意味着去抄袭被人的代码。**千万不要**用复制粘贴的方式编程。

----

汝等…

**千万不要**调用-stringWithString *除非*你：

- 把NSString转换成NSMutableString
- 把NSMutableString转换成NSString
- 需要手工生成一个不可变的NSString*对像
- 确实需要拷贝一个NSMutableString，因为你打算分开编辑它

为什么？

NSStings是不可变的。除非你需要一个mutable的拷贝，你应该永远也不需要一个NSString的拷贝，或者保证我想要保存的NSString指针不是NSMutableString的（那样的话它的值有可能被其他地方的代码改变的）。事实上，copy一个NSString只是简单的在原来string的引用计数上＋1,返回的指针还是原来的string的地址。

-----

汝等…

**总是**对一些有可变（mutable）子类property使用（copy）的存储规则（比如NSString或者NSArrays）。

为什么？

copy方式存储的property（-copy方法也是）都会对这些对象创建一个不可变的拷贝。所以你可以当作是一个不可变的拷贝，但是你不能认为是原来的那个对象不可变。


----

汝等...

当你重载了一个在父类中的方法，**总是**要去调用一下父类的实现，即使你很确信父类的实现中什么都没做，除非你有充足的理由不需要调用父类的实现，如果那样的话你一定要注释清除你这么做的理由。


为什么？

父类的实现可能在将来会做一些事情。其他的一些类可能会在你的类和父类的继承关系之间插入进来，并且做一些实现。

当程序除非方法书重载的不可能去调用父类实现的时候，你应该让你的代码文档更加完整一点。

这样不好

```objc
@implementation Foo
 
- (void)awakeFromNib
{
        // do my setup
}
 
@end
```

这样更好

```objc
@implementation Foo
 
- (void)awakeFromNib
{
        [super awakeFromNib];
 
        // do my setup
}
 
@end
```

-------

汝等…

在条件语句中，**不要**把指针或者数字当做布尔型来对待。

为什么？

布尔有两个值，true或者false（在Objective-C中习惯用YES或者NO）。从另一方面来说，指针可以表示有值，或者是nil。数值可以表示0或有其他值。然而在条件上下文中把0或者nil估计成false，这是一种不明确的强制转型会造成你的代码的语义不容易被理解。所以应该清楚的把指针把当成nil，把整型当成0,把浮点当成0.0。

这样不好…

```objc
- (void)fooWithBar:(Bar*)bar baz:(BOOL)baz quux:(float)quux
{
        if(bar && baz && quux) {
                // do something interesting
        }
}
```

这样更好

```objc
- (void)fooWithBar:(Bar*)bar baz:(BOOL)baz quux:(float)quux
{
        if(bar != nil && baz && quux != 0.0) {
                // do something interesting
        }
}
```

---

汝等…

**不要**在没有必要的时候检查指针时候为空。在Objective-C是比较特殊的，当向一个nil的对象发送任何消息的时候是不会有任何操作的，所以只要按照正常的情况来写你的代码就好了。

这样不好

```objc
- (void)setObj:(NSObject*)obj
{
        if(obj_ != nil) {
                [obj_ doCleanup];
        }
        if(obj != nil) {
                [obj doSetup];
        }
        obj_ = obj;
}
```

这样比较好：

```objc
- (void)setObj:(NSObject*)obj
{
        [obj_ doCleanup]; // Does nothing if obj_ == nil
        [obj doSetup]; // Does nothing if obj == nil
        obj_ = obj;
}
```




[KLUDGE]: https://en.wikipedia.org/wiki/KLUDGE
[ARC]: https://developer.apple.com/library/ios/#releasenotes/ObjectiveC/RN-TransitioningToARC/Introduction/Introduction.html
[v2ex]: https://www.v2ex.com/t/31217
