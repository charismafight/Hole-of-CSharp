# 协程在Unity中的应用

Unity的协程系统基于C#的IEnumerator接口。

## Why
Unity中会大量使用到虽然在每帧中需要处理，但各自独立的事情
```
//一个简单的定时器例子，刷新计时器经过3秒后处理事情

public float timer = 3;
void Update()
{
timer -= Time.deltaTime;
if(timer <= 0)
Debug.Log("Timer has finished!");
}
```

当我们需要处理一个相对比较复杂的类时，就出现了大量的计时器判断、等等难看的代码。另外当必要的时候还需要人为的去把计时器重置。

在优化此类代码的时候，最初的思路是利用循环来优化

```
for(float timer=3;timer>=0;timer-=Time.deltaTime)
{
    //action
}
```
优化之后在update[update例子不合理，应该是用for来代替update]中会以for块为行为的代码块。协程和IEnumerator则是把update和for结合起来，构造成update中调用协程方法的编码模式。


一个简单的协程例子，在脚本开始执行时会启动枚举器，然后一直执行到for外的日志。所以枚举器的yield return实际上是告知线程暂时执行下一帧，知道for循环跳出后结束。
```
    void Start()
    {
        StartCoroutine(CountDown());
    }

        IEnumerator CountDown()
    {
        for (float floatTimer = 3; floatTimer >= 0; floatTimer -= Time.deltaTime)
        {
            Debug.Log(floatTimer);
            //经测试，这里return 任何可通过编译的内容都是一样的结果。
            yield return 0;
        }

        Debug.Log("this message appears after 3 seconds");
    }
```

协程会把方法状态保存下来，所以我们在StartCoroutine方法的参数(函数)中定义的局部变量是可以保存状态的，那么我们就不需要定义一大堆的全局公有或者私有变量来污染类了。


# 用yield return嵌套Coroutine达到同步的效果
```
    void Start()
    {
        StartCoroutine(Wait(1));
        Debug.Log("start 2");
        StartCoroutine(Wait(2));
        Debug.Log("start 3");
        StartCoroutine(Wait(3));
        //StartCoroutine(SaySomething());
    }
```
上面的例子的运行结果可以看到当第一个协程首次返回后Debug.Log("start 2");就被执行了，所以可以简单的把startcoroutine看成是一个多线程操作，那么当3个协程都开始运行之后就无法在start()函数中用代码顺序来控制协程的执行了。从测试的结果来看后续都是规律的（3，1，2）打印，推测是每一帧都固定化了协程中的函数执行顺序。而这个例子显然没有达到我们期望的wait计时器效果，所以可以使用yield return StartCoroutine()来确保执行完毕后才返回继续。
这个东西在Unity这种执行逻辑下（也可能是所有游戏开发下）很有用，因为游戏开发脚本多数场景下都可以认为是无限在循环(Update)，那么行为时间-行为的控制就比较依赖全局变量或者协程了。