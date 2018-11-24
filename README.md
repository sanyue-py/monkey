# monkey
monkey 基础知识1
什么是monkey，它能干什么？

Monkey术语叫“猴子测试”，是指没有测试经验的人进行的测试是毫无目的，乱按乱点。

Monkey是Android系统自带的一个命令行测试工具。通过monkey用随机重复的方法对应用程序进行一些稳定性，健壮性方面的测试，是一种快速有效的测试方法。

 

Monkey测试本身非常简单，就是模拟用户的按键输入，触摸屏输入，手势输入等，看设备多长时间会出异常。原理就是向被测试的程序发送伪随机的用户事件流。

 
测试结果如何分析？

一.   初步分析方法：

Monkey测试出现错误后，一般的差错步骤为以下几步：

1、 找到是monkey里面的哪个地方出错

2、 查看Monkey里面出错前的一些事件动作，并手动执行该动作

3、 若以上步骤还不能找出，可以使用之前执行的monkey命令再执行一遍，注意seed值要一样。

一般的测试结果分析：1、 ANR问题：在日志中搜索“ANR”

2、 崩溃问题：在日志中搜索“Exception”

 

二.   详细分析monkey日志：

将执行Monkey生成的log，从手机中导出并打开查看该log；在log的最开始都会显示Monkey执行的seed值、执行次数和测试的包名。首先我们需要查看Monkey测试中是否出现了ANR或者异常，具体方法如上述。然后我们要分析log中的具体信息，方法如下：查看log中第一个Switch，主要是查看Monkey执行的是那一个Activity，譬如下面的log中，执行的是 com.tencent.smtt.SplashActivity，在下一个swtich之间的，如果出现了崩溃或其他异常，可以在该Activity中查找问题的所在。:Switch:#Intent;action=android.intent.action.MAIN;category=android.intent.category.LAUNCHER;launchFlags=0x10000000;component=com.tencent.smtt/.SplashActivity

 

具体问题将在后面详细分析。

 
具体安装与操作演示（windows下）

1.      执行monkey都需要什么基础玩意呢？

因为Monkey是个命令行执行的，所以我们需要执行工具

这个工具叫adb.exe，那么adb如何执行呢？当然是cmd.exe!

 

Cmd.exe 里执行 adb shell 查看本机是否有adb.

 

推荐绑定环境变量。

比如我的地址是：

D:\tools\adt-bundle-windows-x86_64-20140702\sdk\platform-tools

将此右击 “我的电脑”à “属性”à “高级设置”à “环境变量”

Path里加入上面地址。

然后cmd，输入adb.exe试试吧，一定要重新打开cmd。

 

2.      运行adb sheel monkey

在cmd里运行：adb shell monkey

这是没有虚拟机或者没有连接设备。

当你连接设备后出下面提示：

 

我想查看当前手机都装了什么软件？

步骤：

1.      adb shell

2.      su root

3.      cd data

4.      cd data

5.      ls

将列出所有安装的软件的包名！爽吧？

 

悲催的事1：运行su root的时候：

pc进入android手机shell需要root权限(su: notfound)

“网上说，因为操作系统本来就没有添加root用户，需要下载一个一键root来生成root用户；下了个，安装后，运行一键root；再运行 su，果然好使了，变成 #了。”

 

3.      简单的monkey测试---测试日历程序

前文书说过我查看手机都装啥软件的时候提示我shell命里需要root权限。

Monkey是不需要root权限的，可以直接用。

 

我已知日历程序的包名是：com.android.calendar

输入：adb shell monkey –p com.android.calendar 100

如图：

在你的设备上将看到：日历被启动，噼里啪啦的一顿乱变！并在测试后停到最终页面。

如图：

 

4.      如何查看monkey执行过程信息?

通过 –v 参数。

通过-v参数可以指定打印信息的详细级别，多一个-v多一个级别！

级别
	

表示
	

描述

0
	

-v
	

打印最基本的事件

1
	

-v-v
	

更全面的信息，如增加了那些Activity被选中

2
	

-v-v-v
	

最全面的信息

 

举例：

还是日历程序，我们看看-v都返回了什么信息。

Cmd输入: adb shell monkey -v -p com.android.calendar 100

结果如图：

 

具体解读：

1.      Seed字段

我们刚才执行monkey时，没有指定seed，在报告一开始，可以看到其自动生成的seed值，如果我们指定了seed，则可以使用相同的seed来生成相同的序列。注意：不能完全重复上次操作，但是具体操作事件是相同的。比如点击->向左拖动->释放这几个事件这是相同的，但具体涉及的坐标是不相同的。

   

2.      AllowPackage字段

只允许com.android.calendar包中的Activity(活动)

 

3.      IncludeCategory字段

启动的意图

 

4.      本次伪随机事件中各种类型的事件比例

这里将事件分为了10种类型，分别为：

0：触摸事件百分比，即参数--pct-touch

1：滑动事件百分比，即参数--pct-motion

2：缩放事件百分比，即参数--pct-pinchzoom

3：轨迹球事件百分比，即参数--pct-trackball

4：屏幕旋转事件百分比，

5：基本导航事件百分比，即参数--pct-nav

6：主要导航事件百分比，即参数--pct-majornav

7：系统事件百分比，即参数--pct-syskeys

8：Activity启动事件百分比，即参数--pct-appswitch

9：键盘翻转事件百分比，即参数--pct-flip

10：其他事件百分比，即参数--pct-anyevent

更为具体的类型信息，可以查看查看Monkey源码中

 

5.      Switch

表示跳转到com.android.calendar里的AllInOneActivity里。

 

6.      //Allowing start of Intent

表示允许启动这个活动意图。

 

7.      Sleeping

允许在发送各种随机事件时有一个延时。如果没有表示立即执行，不停顿。

                  

8.      Sending:

具体执行的随机事件。

 

9.      injected

执行100次具体事件

 

10.  rotation

屏幕旋转信息，角度和是否保持旋转状态。

 

11.  Dropped

丢弃的事件。

 

12.  网络耗时

包括：手机占用的，wifi占用的，连接占用的。

 

5.      如何查看anr,崩溃问题？

在注意查找日志中的无响应问题和崩溃问题。

无响应问题可以在日志中搜索 “ANR”，崩溃问题搜索 “CRASH”，内存泄露问题搜索"GC"（需进一步分析），异常问题搜索 “Exception”（如果出现空指针， NullPointerException，需格外重视）。

 

6.      Monkey各类事件（参数）

常规
--help
列出简单的用法。

-v
命令行的每一个-v将增加反馈信息的级别。Level 0(缺省值)除 启动提示、测试完成和最终结果之外，提供较少信息。Level 1提供较为详细的测试信息，如逐个发送到Activity的 事件。Level 2提供更加详细的设置信息，如测试中被选中的或未被选中的Activity。

事件
-s <seed>
伪随机数生成器的seed值。如果用相同的seed值再次运行Monkey， 它将生成相同的事件序列。

例子：

adb shell monkey -v -p com.android.calendar –s 404 100

如果两次执行，那么事件相同。

--throttle <milliseconds>
在事件之间插入固定延迟。通过这个选项可以减缓Monkey的执行速度。如果不指定该选项，Monkey将 不会被延迟，事件将尽可能快地被产成。
例子：

adb shell monkey -v -p com.android.calendar –throttle 1000 100

表示每次事件间隔1秒。


--pct-touch <percent>
调整触摸事件的百分比(触摸事件是一个down-up事件，它发生在屏幕上的某单一 位置)。

--pct-motion <percent>
调整动作事件的百分比(动作事件由屏幕上某处的一个down事件、一系列的伪随机事 件和一个up事件组成)。

--pct-trackball <percent>
调整轨迹事件的百分比(轨迹事件由一个或几个随机的移动组成，有时还伴随有点击)。

--pct-nav <percent>
调整“基本”导航事件的百分比(导航事件由来自方向输入 设备的up/down/left/right组成)。

--pct-majornav <percent>
调整“主要”导航事件的百分比(这些导航事件通常引发图 形界面中的动作，如：5-way键盘的中间按键、回退按键、菜单按键)

--pct-syskeys <percent>
调整“系统”按键事件的百分比(这些按键通常被保留，由 系统使用，如Home、Back、StartCall、End Call及音量控制键)。

--pct-appswitch <percent>
调整启动Activity的百分比。在随机间隔里，Monkey将执行一个startActivity()调 用，作为最大程度覆盖包中全部Activity的一种方法。

--pct-anyevent <percent>
调整其它类型事件的百分比。它包罗了所有其它类型的事件，如：按键、其它不常用的设备按钮、等等。

约束限制
-p <allowed-package-name>
如果用此参数指定了一个或几个包，Monkey将只允许系统启动这些包里的Activity。 如果你的应用程序还需要访问其它包里的Activity(如选择取一个联系人)，那些包也需要在此 同时指定。如果不指定任何包，Monkey将允许系统启动全部包里的Activity。要指定多个 包，需要使用多个 -p选项，每个-p选项只能用于一个包。

-c <main-category>
如果用此参数指定了一个或几个类别，Monkey将只允许系统启动被这些类别中的某个类别列出的Activity。 如果不指定任何类别，Monkey将选 择下列类别中列出的Activity： Intent.CATEGORY_LAUNCHER或Intent.CATEGORY_MONKEY。要指定多 个类别，需要使用多个-c选项，每个-c选 项只能用于一个类别。

调试
--dbg-no-events
设置此选项，Monkey将执行初始启动，进入到一个测试Activity，然后不 会再进一步生成事件。为了得到最佳结果，把它与-v、一个或几个包约 束、以及一个保持Monkey运 行30秒或更长时间的非零值联合起来，从而提供一个环境，可以监视应用程序所调用的包之间的转换。

--hprof
设置此选项，将在Monkey事件序列之前和之后立即生成profiling报告。 这将会在data/misc中生成大文件(~5Mb)，所以要小心使用它。

--ignore-crashes
通常，当应用程序崩溃或发生任何失控异常时，Monkey将停止运行。如果设置此选项，Monkey将 继续向系统发送事件，直到计数完成。

--ignore-timeouts
通常，当应用程序发生任何超时错误(如“Application Not Responding”对 话框)时，Monkey将停止运行。如果设置此选项，Monkey将继 续向系统发送事件，直到计数完成。

--ignore-security-exceptions
通常，当应用程序发生许可错误(如启动一个需要某些许可的Activity)时，Monkey将 停止运行。如果设置了此选项，Monkey将继续向系统发送事件，直到计数完成。

--kill-process-after-error
通常，当Monkey由于一个错误而停止时，出错的应用程序将继续处于运行状态。当设置了此选项时，将会通知系 统停止发生错误的进程。注意，正常的(成功的)结束，并没有停止启动的进程，设备只是在结束事件之 后，简单地保持在最后的状态。

--monitor-native-crashes
监视并报告Android系统中本地代码的崩溃事件。如果设置了--kill-process-after-error， 系统将停止运行。

--wait-dbg
停止执行中的Monkey，直到有调试器和它相连接。

 

         综合示例：

adb shell monkey -v -v --ignore-crashes --ignore-timeouts --kill-process-after-error-p com.android.calendar --throttle 500 -s 404 100

 

7.      如何将结果输出到文件？

只需要 在后面加上>路径即可。

例：adb shell monkey -v -p com.android.calendar 100 >D:/calendar_1.txt

 

8.      Monkey也是可以做自动化测试的，你知道不？

后续补充。

难点在于你要知道都有什么控件，叫啥名字，位置。但是这些是可以解决的，有一个工具叫：uiautomatorviewer 在sdk/tools/uiautomatorviewer.bat.
--------------------- 
作者：lxmhuendan 
来源：CSDN 
原文：https://blog.csdn.net/lxmhuendan/article/details/54946893 
版权声明：本文为博主原创文章，转载请附上博文链接！
