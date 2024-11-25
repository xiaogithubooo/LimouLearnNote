# 1.Android 开发架构

-   **Linux 内核** 在所有层的最底下是 `Linux`，提供了基本的系统功能，比如进程管理，内存管理，设备管理（如摄像头，键盘，显示器）。同时，内核处理所有 `Linux` 所擅长的工作，如网络和大量的设备驱动，从而避免兼容大量外围硬件接口带来的不便。

-   **其他程序库** 在 `Linux` 内核层的上面是一系列程序库的集合，包括开源的 `Web` 浏览器渲染引擎 `Webkit` ，知名的 `libc` 标准库（`C` 语言的），用于仓库存储和应用数据共享的轻量级 `SQLite` 数据库，用于播放、录制音视频的音视频库，用于网络安全的 `SSL` 网络安全库...

-   **安卓程序库** 这个类别包括了专门为 `Android` 开发的基于 `Java` 的程序库。这个类别程序库的示例包括应用程序框架库，如用户界面构建，图形绘制和数据库访问。一些 `Android` 开发者可用的 `Android` 核心程序库总结如下：

    (1)android.app - 提供应用程序模型的访问，是所有 Android 应用程序的基石。

    (2)android.content - 方便应用程序之间，应用程序组件之间的内容访问，发布，消息传递。

    (3)android.database - 用于访问内容提供者发布的数据，包含 SQLite 数据库管理类。

    (4)android.opengl - OpenGL ES 3D 图片渲染 API 的 Java 接口。

    (5)android.os - 提供应用程序访问标注操作系统服务的能力，包括消息，系统服务和进程间通信。

    (6)android.text - 在设备显示上渲染和操作文本。

    (7)android.view - 应用程序用户界面的基础构建块。

    (8)android.widget - 丰富的预置用户界面组件集合，包括按钮，标签，列表，布局管理，单选按钮等。

    (9)android.webkit - 一系列类的集合，允许为应用程序提供内建的 Web 浏览能力。

-   **运行时** 提供名为 `Dalvik` 虚拟机的关键组件，类似于 `Java` 虚拟机，但专门为 `Android` 设计和优化.`Dalvik` 虚拟机使得可以在 `Java` 中使用 `Linux` 核心功能，如内存管理和多线程。`Dalvik` 虚拟机使得每一个 `Android` 应用程序运行在自己独立的虚拟机进程。`Android` 运行时同时提供一系列核心的库来为 `Android` 应用程序开发者使用标准的 `Java` 语言来编写 `Android` 应用程序。

-   **应用框架** 应用框架层以 `Java` 类的形式为应用程序提供许多高级的服务。应用程序开发者被允许在应用中使用这些服务。

    (1)活动管理者，控制应用程序生命周期和活动栈的所有方面

    (2)内容提供者，允许应用程序之间发布和分享数据

    (3)资源管理器，提供对非代码嵌入资源的访问，如字符串，颜色设置和用户界面布局

    (4)通知管理器，允许应用程序显示对话框或者通知给用户

    (5)视图系统，一个可扩展的视图集合，用于创建应用程序用户界面

-   **应用程序** 顶层中有所有的 `Android` 应用程序，您写的应用程序也将被安装在这层。这些应用程序包括通讯录，浏览器，游戏等。

# 2.Android 应用组件

应用程序组件是一个 `Android` 应用程序的基本构建块，以下是可以在 `Android` 应用程序中使用的四个主要组件。

| 组件                        | 描述                                        |
| :-------------------------- | :------------------------------------------ |
| Activities（活动）          | 描述 `UI`，并且处理用户与机器屏幕的交互     |
| Services（后台）            | 处理与应用程序关联的后台操作                |
| Broadcast Receivers（通信） | 处理 `Android` 操作系统和应用程序之间的通信 |
| Content Providers（数据）   | 处理数据和数据库管理方面的问题              |

-   **Activities** 一个活动标识一个具有用户界面的单一屏幕。举个例子，一个邮件应用程序可以包含一个活动用于显示新邮件列表，另一个活动用来编写邮件，再一个活动来阅读邮件。当应用程序拥有多于一个活动，其中的一个会被标记为当应用程序启动的时候显示。一个活动一定是 `Activity` 类的一个子类。

    ```java
    public class MainActivity extends Activity {}
    ```
    
-   **Services** 服务是运行在后台，执行长时间操作的组件。举个例子，服务可以是用户在使用不同的程序时在后台播放音乐，或者在活动中通过网络获取数据但不阻塞用户交互。一个服务一定是 `Service` 类的子类。

    ```java
    public class MyService extends Service {}
    ```

-   **Broadcast Receivers** 广播接收器简单地响应从其他应用程序或者系统发来的广播消息。举个例子，应用程序可以发起广播（交给操作系统）来让其他应用程序知道一些数据已经被下载到设备，并且可以供他们使用。因此广播接收器会拦截这些通信并采取适当的行动。广播接收器一定是 `BroadcastReceiver` 类的一个子类，每个消息以 `Intent` 对象的形式来广播。

    ```java
    public class MyReceiver  extends  BroadcastReceiver {}
    ```
    
-   **Content Providers** 内容提供者组件通过请求从一个应用程序到另一个应用程序提供数据。这些请求由 `ContentResolver` 类的方法来处理。这些数据可以是存储在文件系统、数据库或者其他其他地方。内容提供者一定是 `ContentProvider` 类的子类，并实现一套标准的 `API`，以便其他应用程序来执行事务。

    ```java
    public class MyContentProvider extends  ContentProvider {}
    ```

>   [!IMPORTANT]
>
>   补充：附件组件
>
>   有一些附件的组件用于以上提到的实体、他们之间逻辑、及他们之间连线的构造。这些组件如下：
>
>   | 组件      | 描述                                             |
>   | :-------- | :----------------------------------------------- |
>   | Fragments | 代表活动中的一个行为或者一部分用户界面。         |
>   | Views     | 绘制在屏幕上的 UI 元素，包括按钮，列表等。         |
>   | Layouts   | 控制屏幕格式，展示视图外观的 View 的继承。         |
>   | Intents   | 组件间的消息连线。                               |
>   | Resources | 外部元素，例如字符串资源、常量资源及图片资源等。 |
>   | Manifest  | 应用程序的配置文件。                             |

我们使用 `AS` 创建一个项目试试，然后重点了解项目的文件树、主活动文件、清单文件等，我们来看看那些事重要的文件。

| 文件夹、文件和说明                                           |
| :----------------------------------------------------------- |
| `AndroidManifest.xml:` 这个是应用程序的清单文件，描述了应用程序的基础特性，定义它的各种组件（三元素是活动行为，活动类别，活动数据）。 |
| `src:` 包含项目中所有的.java 源文件，默认情况下，它包括一个 MainActivity.java 源文件对应的活动类，当应用程序通过应用图标启动时，将运行它。 |
| `gen:` 这包含由编译器生成的.R 文件，引用了所有项目中的资源。该文件不能被修改。 |
| `bin:` 这个文件夹包含 Android 由 APT 构建的 .apk 包文件，以及运行 Android 应用程序所需要的其他所有东西。 |
| `res/drawable-hdpi:` 这个目录下包括所有的为高密度屏幕设计所需的 drawable 对象。 |
| `res/layout:` 这个目录存放用于定义用户界面的文件。           |
| `res/values:` 这个目录存放各种各样的包含一系列资源的 XML 文件，比如字符串和颜色的定义。 |

```shell
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.App"
        tools:targetApi="31">
        <activity
            android:name=".MainActivity" #  声明活动类名, 以 . 开头表示此类在当前包下
            android:exported="true"> # 指示其他应用是否可以启动此活动
            <intent-filter> # 意图过滤器
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
                # android.intent.action.MAIN: 表示这是应用的主入口。
# android.intent.category.LAUNCHER: 表示此活动会在应用启动器中显示。
            </intent-filter>
        </activity>
    </application>

</manifest>
```

