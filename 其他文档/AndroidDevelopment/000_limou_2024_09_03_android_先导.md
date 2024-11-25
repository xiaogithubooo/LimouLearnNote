安卓开发的资料还是很充足的，安卓开发主要是偏向移动端的开发，这里简单接受一下安卓开发的路线和资源。首先最重要的就是[前往 Google 自家的 Android 官网](https://developer.android.com/?hl=zh-cn)，里面有非常丰富的文档。

而学习 `Java` 的安卓开发有两个官方教程：

-   [Android 开发者基础知识 (Java)](https://developer.android.com/courses/fundamentals-training/overview-v2?hl=zh-cn)
-   [高级 Android 开发 (Java)](https://developer.android.com/courses/advanced-training/overview?hl=zh-cn)

并且有一个项目值得您去实现和学习，就是 [由 Google 开发者培训团队创建的电话呼叫和短信课程项目](https://google-developer-training.github.io/android-developer-phone-sms-course/)。

# 1.语言和环境

安卓开发上，首推的编程语言就是 `Java, Kotlin, Dart` 三种语言，可能还需要学习 `UI` 工具包 `Flutter`，以及关于 `AS` 的使用。

本教程使用 `Java` 和 `Kotlin` 两种开发语言进行尝试，不过优先使用 `Java`，再来考虑 `Kotlin`。

# 2.Android 四大开发组件

在 `Android` 开发中，这四个组件和概念是构建和管理应用程序的核心组成部分。

1.   **Activity** 是 `Android` 应用的单一屏幕，用户界面组件的核心部分。每个 `Activity` 代表一个屏幕，例如一个表单、一个列表或一个设置页面
2.   **Service** 是一个可以在后台执行长时间运行操作的组件，即使用户界面没有显示。它用于处理长时间运行的任务，例如播放音乐、下载文件或处理网络请求
3.   **Broadcast Receiver** `Broadcast Receiver` 用于接收和处理全局广播消息。这些消息可以来自系统或其他应用，广播接收器可以响应特定的事件，例如屏幕开关、网络状态变化或其他系统事件
4.   **Content Provider** `Content Provider` 是一种管理应用数据并允许其他应用访问的组件。它提供了一种标准的接口来进行数据存取，应用可以通过 `Content Provider` 进行数据共享。

这些组件和概念在 `Android` 开发中各自承担着不同的功能，共同支持了应用的各个方面，包括用户界面、后台任务、系统消息处理、数据共享。

关于上述的知识，[这本 《第一行代码：Android》 您可以快速入门一下](https://weread.qq.com/web/reader/73532150723f022f73516a6ke4d32d5015e4da3b7fbb1fa)。

# 3.Intent 机制

待补充...

>   以上内容实际上是参考 `Medium` 上 `@MindOrks` 发布的一篇 [2020 年 Android 程序员的学习线路](https://medium.com/mindorks/android-development-learning-path-2020-edition-3f464ac56dbf)，我认为非常具有参考意义，您值得一看。

