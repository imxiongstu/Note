# C#如何抓包？FiddlerCore的基本使用（1）
> 其实写这篇文章，是因为之前那个《羊了个羊》游戏的爆火，然后一直手动用Fiddler抓包来进行修改数据，但还是太麻烦，就自己写了一个程序，自动抓包并修改，现将基础使用写成文章，供大家参考。

## 基本条件
* 首先需要下载`FiddlerCore.dll`这个包，可以直接通过命令行
`dotnet add package FiddlerCore --version 4.6.2`
安装该包，这个直接在nuget里面是找不到的，因为这个是收费的包，但2016年的这个包还是可以用，符合`.NetStandard 2.0`标准， 所以基本上目前版本的程序框架都是可以用的。

## 基本使用
* 启动监听
  ```cs
    //TODO 启动监听（监听端口，是否注册成系统代理，解密SSL，允许远程源）
    FiddlerApplication.Startup(9898, true, true, true);
  ```
    需要注意的是，`是否注册成系统代理`为`true`的话，基本上就相当于是监听整个系统的请求，因为如果一个程序，你没有指定它使用的网络代理的话，那它默认走的就是系统代理，如果为`false`的话，你想抓包那个程序，就需要手动去指定那个程序走的代理端口了。

* 启动以后，就可以开始抓包了
   
  ```cs
    //TODO 请求前事件
    FiddlerApplication.BeforeRequest += session =>
    {
        Console.WriteLine(session);
        var beforeSession = session;
    };

    //TODO 整个会话请求结束之后事件
    FiddlerApplication.AfterSessionComplete += session =>
    {
        Console.WriteLine(session);
        var afterSession = session;
    };
  ```
    注册完事件以后，如果一旦有请求，就会自动响应，我们看一下抓到的会话数据
    ![图片](https://www.imxiongstu.cn/usr/uploads/2022/10/456140175.png)
    基本上该有的Http请求的会话数据都可以抓到

* 最后注意
    程序关闭前一定要先`停止抓包`，不然的话，后续是没办法访问相关Http/Https请求的，因为之前已经注册成了`系统代理`，所以关闭这个程序了，需要在关闭之前恢复原本系统代理。
    ```cs
    //TODO 停止监听
    if (FiddlerApplication.IsStarted())
    {
        FiddlerApplication.Shutdown();
    }
    ```
> 基本的捕获就是目前这样，后续应该还会持续更新，可以关注一下。