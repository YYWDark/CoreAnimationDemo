http://www.jianshu.com/p/1efb0238c1dd
![封面.jpg](http://upload-images.jianshu.io/upload_images/307963-1abae31bf84ffe4b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##前言：
核心动画是iOS中一个渲染和动画的基础设施，你可以用它来为你的应用做基础的动画效果。在核心动画中，你要做的只是配置一些动画参数，接下来的任务就可以交给核心动画来处理了，很方便是吧。下面是核心动画在系统的层次结构：
![1.png](http://upload-images.jianshu.io/upload_images/307963-a44e7c8f759ea65d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
###CoreAnimation
`CAAnimation` 是一个抽象的动画类，他提供基本的支持给`CAMediaTiming`和`CAAction`。给`Layer`添加动画，你也可以使用子类`CABasicAnimation`,` CAKeyframeAnimation`, `CAAnimationGroup`, 和 `CATransition`.

![2.png](http://upload-images.jianshu.io/upload_images/307963-a7e867ac870a2655.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###CAAnimation
**CAMediaTimingFunction**
是一个控制动画节奏的可选的时间函数。它的初始化以下面的初始化方式构造对象:
```
+ (instancetype)functionWithName:(NSString *)name;
/** Timing function names. **/

CA_EXTERN NSString * const kCAMediaTimingFunctionLinear
    __OSX_AVAILABLE_STARTING (__MAC_10_5, __IPHONE_2_0);
CA_EXTERN NSString * const kCAMediaTimingFunctionEaseIn
    __OSX_AVAILABLE_STARTING (__MAC_10_5, __IPHONE_2_0);
CA_EXTERN NSString * const kCAMediaTimingFunctionEaseOut
    __OSX_AVAILABLE_STARTING (__MAC_10_5, __IPHONE_2_0);
CA_EXTERN NSString * const kCAMediaTimingFunctionEaseInEaseOut
    __OSX_AVAILABLE_STARTING (__MAC_10_5, __IPHONE_2_0);
CA_EXTERN NSString * const kCAMediaTimingFunctionDefault
    __OSX_AVAILABLE_STARTING (__MAC_10_6, __IPHONE_3_0);
```
具体的效果可以看下面的图。`kCAMediaTimingFunctionDefault`是系统默认的时间函数。使用这个函数来确保你的动画时间匹配大多数系统的动画。
![3.png](http://upload-images.jianshu.io/upload_images/307963-4aeaa285ed30fcbf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


你可以对应上图看下面的效果：
![4.gif](http://upload-images.jianshu.io/upload_images/307963-c341fd4acc6acb96.gif?imageMogr2/auto-orient/strip)


你可以点击[这里](https://zsisme.gitbooks.io/ios-/content/chapter10/custom-easing-functions.html)是获取`CAMediaTimingFunction`更多相关内容。你也可以点击[这里](http://cubic-bezier.com/#0,1.03,1,0)自己去操作一下相关内容。

**CAAnimationDelegate**
每个动画都有一个`delegate`,会回调给你一个动画开始和动画结束的协议。方便为你的应用做相应的处理。在动画结束后你应该为`layer`使用`removeAllAnimations`移除相应的动画，减小不必要的开销。
```
#pragma  mark - CAAnimationDelegate
- (void)animationDidStart:(CAAnimation *)anim{
    NSLog(@"动画开始");
}

- (void)animationDidStop:(CAAnimation *)anim finished:(BOOL)flag {
   
   /*
    因为我们的动画repeatCount属性设置成了1000。所以会很久才结束。你可以手动修改
    你可以根据"key值来判断是哪个layer上的动画"
    比如：[XXX.arrowLayer animationForKey:@"positionX"]拿到动画
    */
   NSLog(@"动画结束");
}
```
具体相关的内容可以见demo中的`CAAnimationViewController`

###CAPropertyAnimation
`CAPropertyAnimation`是`CAAnimation`的子类，创造动画的时候以`key-path`操作相关视图层的属性来完成动画。
完整的`key-path`请点击[这里](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CoreAnimation_guide/Key-ValueCodingExtensions/Key-ValueCodingExtensions.html)
具体使用`key-path`操作可以见demo中的`CAPropertyAnimationViewController`。
```
属性值
**additive** 决定是否把动画指定的值添加到当前render tree 来产生新的render tree值。
```
`additive`的解释还是太过于生硬，你需要知道`layer tree`（见最下面的layer tree模块）。在动画的时候设置`additive` YES后会把当前的`presentation layer`加入到`render tree`。

#####CABasicAnimation
`CABasicAnimation`对象为`layer`提供基本的，单一的frame有关的动画。CABasicAnimation里面需要注意`fromValue`，`byValue`，`toValue` 属性控制




设置预备的参数：

| 组合参数（不为nil）        | 区间值   |
| --------   | -----:  |
| fromValue + toValue |[fromValue,toValue]  | 
| fromValue   + byValue   |     [fromValue,fromValue + byValue]|   
| byValue + toValue|    [toValue - byValue,toValue] |   
| fromValue |    [fromValue ,layer当前设置的value] |  
| toValue |    [layer当前设置的value ,toValue] | 
| byValue |    [layer当前设置的value ,layer当前设置的value + byValue] | 

#####CAKeyframeAnimation
`CAKeyframeAnimation`对象有能力为`layer`对象提供关键帧动画。你可以通过指定一个储存关键帧的数组来控制相应的动画时间和动画行为。

```
属性值
**values**     一个承载动画多个关键帧值的数组
**keyTimes**  一个承载动画时间的数组。数据的每个值都在0-1之间，第一个值为0最后的一个值为1。数组的值对应关键帧数组的值或者是path路劲的属性
**path**  一段CGPathRef路径。layer沿着路劲动画。如果你同时设置了values 和path，path路劲将会被执行。path拥有更高的优先权。
```
具体使用方法可以见demo中的`CAKeyframeAnimationViewController`，雪人是按照`path`路径，社交图标是按照`values` 效果：

![5.gif](http://upload-images.jianshu.io/upload_images/307963-cb8f100801c8505c.gif?imageMogr2/auto-orient/strip)


##CATransaction
对`layer`每一次的修改都是事务的一部分。事物是通过`CATransaction`类来管理的。`CoreAnimation` 支持两种类型的事务：隐式事务和显式事务。`CATransaction`类不是通过`alloc init`方法来初始化。而是借助于`begin` 和`commit`方法。
```
method：
**begin**  为当前线程创建一个新的事务
**commit** 提交当前的事务所有的改变项
```
看`demo`中`CATransactionViewController`类：
```
- (void)viewDidLoad{
    [super viewDidLoad];
    self.view.backgroundColor = [UIColor whiteColor];
    
    self.imageView = [[UIImageView alloc] initWithImage:[UIImage imageNamed:@"圣诞老人"]];
    self.imageView.frame = CGRectMake(0, 0, 100, 100);
    self.imageView.center = self.view.center;
    [self.view addSubview:self.imageView];
    
    self.redLayer = [CALayer layer];
    self.redLayer.frame = CGRectMake(100, 100, 100, 100);
    self.redLayer.delegate = self;
    self.redLayer.backgroundColor = [UIColor redColor].CGColor;
    [self.view.layer addSublayer:self.redLayer];
    
}

- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event
{
//    configure the transaction
    [CATransaction begin];
//    [CATransaction setDisableActions:YES];
    [CATransaction setAnimationDuration:4.0];
    [CATransaction setAnimationTimingFunction:[CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseOut]];
    //set the position
    self.redLayer.position  = [[touches anyObject] locationInView:self.view];
    self.imageView.layer.position = [[touches anyObject] locationInView:self.view];
   
    //commit transaction
    [CATransaction commit];
    
    
    NSLog(@"Outside: %@", [self.imageView actionForLayer:self.imageView.layer forKey:@"position"]);
    //begin animation block
    [UIView beginAnimations:nil context:nil];
    //test layer action when inside of animation block
    NSLog(@"Inside: %@", [self.imageView actionForLayer:self.imageView.layer forKey:@"position"]);
    //end animation block
    [UIView commitAnimations];
    
}
```
![6.gif](http://upload-images.jianshu.io/upload_images/307963-d863b2ef73ea705a.gif?imageMogr2/auto-orient/strip)

红色的视图是`layer`.圣诞老人是`UIImageView`。当点击了其他区域，你会发现红色的视图有一个缓慢的动画，而圣诞老人马上就到了指定区域。这是因为`UIView`的隐式动画被关联图层给禁用了。
对于`layer`图层你还可以利用下面方法禁用隐式动画。
```
 [CATransaction setDisableActions:YES];
```
更多信息你可以点击[这里](https://github.com/AttackOnDobby/iOS-Core-Animation-Advanced-Techniques/blob/master/7-%E9%9A%90%E5%BC%8F%E5%8A%A8%E7%94%BB/%E9%9A%90%E5%BC%8F%E5%8A%A8%E7%94%BB.md)去了解。也建议读读喵大翻译的[View-Layer 协作](https://objccn.io/issue-12-4/).


###CAAnimationGroup
`CAAnimationGroup`和`CAPropertyAnimation`一样是`CAAnimation`的子类。`CAAnimationGroup`可以让多个动画组合同时发生。
```
Important
The delegate and removedOnCompletion properties of animations in the [animations
](https://developer.apple.com/reference/quartzcore/caanimationgroup/1412516-animations?language=objc) array are currently ignored. The CAAnimationGroup
 delegate does receive these messages.
```



### layer tree
核心动画中有三种`layer tree`。每一种`layer tree`在你的应用呈现在屏幕中扮演着不同的角色。
> * model layer tree (就是我们应用通常使用最多的layer对象,他们为动画储存着目标值。比如:position,paque等)
> * presentation tree(这些layer对象呈现着一个动画当前值。比如你的layer沿着x轴历时5秒从0到5model layer position会一直显示最后的值5，而presentation随着时间显示0,1,2..5。注意你不应该人为去修改这些值。)
> * render tree（这种layer对象在实际的动画中,但它对核心动画来说是私有的，无法操作的）

![7.png](http://upload-images.jianshu.io/upload_images/307963-61cfcb1128e56dd1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们来验证下，在例子中的`LayerTreeViewController`类中加入下面的代码：
```
- (void)viewDidLoad {
    [super viewDidLoad];
    self.view.backgroundColor = [UIColor whiteColor];
    self.redView.layer.position = CGPointMake(100, 100);
    [self printMethod];
    
    CABasicAnimation *basicAnimation  = [[CABasicAnimation alloc] init];
    basicAnimation.keyPath = @"position.y";
    basicAnimation.fromValue = @0;
    basicAnimation.toValue = @500;
    basicAnimation.duration = 10;
    [self.redView.layer addAnimation:basicAnimation forKey:@"positionY"];
    
 
    NSTimer *timer = [NSTimer scheduledTimerWithTimeInterval:1.0f
                                                      target:self
                                                    selector:@selector(printMethod)
                                                    userInfo:nil
                                                     repeats:YES];
    [timer fire];
    
    
}

- (void)printMethod{
    CALayer *modelLayer = self.redView.layer;
    CALayer *presentationLayer = self.redView.layer.presentationLayer;
    NSLog(@"model layer:%@     presentation layer :%@", NSStringFromCGPoint(modelLayer.position),NSStringFromCGPoint(presentationLayer.position));
}

#pragma mark - get
- (UIView *)redView{
    if (!_redView) {
        _redView = [[UIView alloc] init];
        _redView.frame = CGRectMake(0, 0, 100, 100);
        _redView.backgroundColor = [UIColor redColor];
        _redView.center = self.view.center;
        [self.view addSubview:_redView];
        [self printMethod];
    }
    return _redView;
}
```
看看打印结果：


![3.png](http://upload-images.jianshu.io/upload_images/307963-036c6d1840a8da02.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看出`presentation layer`初始值为（0，0）。当我们手动的去改变`postion`值，`presentation layer`没有发现变化而`model layer`发生了改变。在使用动画的时候`presentation layer`才发生变化，代表在动画中`layer`实时的值。


更多了解您可以点击[Demo](https://github.com/YYWDark/CoreAnimationDemo)。

##参考资料:
> 
###### [官方文档](https://developer.apple.com/reference/quartzcore/caanimation?language=objc)
