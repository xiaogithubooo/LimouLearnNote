将 C++ 程序封装为 Java 代码通常涉及以下步骤和技术：

### 使用 JNI（Java Native Interface）

JNI 是 Java 提供的一种机制，允许 Java 应用程序调用本地（Native）代码，比如 C 或 C++ 编写的代码。通过 JNI，你可以将现有的 C++ 函数封装为 Java 的本地方法，从而在 Java 中调用它们。

#### 步骤概述：

1. **编写 C++ 代码**：编写你的 C++ 函数或类，实现你的功能。

2. **编写 JNI 头文件**：为你的 C++ 函数编写 JNI 头文件（.h 文件），声明 JNI 接口函数。

3. **编写 JNI 实现文件**：实现 JNI 头文件中声明的 JNI 接口函数，将其与 C++ 函数连接起来。

4. **生成动态链接库（DLL 或 SO 文件）**：编译 C++ 代码生成动态链接库，Java 将在运行时加载它。

5. **编写 Java 类**：在 Java 中编写一个类，声明为 native 的本地方法，并加载动态链接库。

6. **编译 Java 代码**：编译 Java 代码。

7. **运行 Java 程序**：运行 Java 程序，它将调用本地方法并执行 C++ 函数的逻辑。

#### 具体步骤和示例：

**1. 编写 C++ 代码**

假设你有一个 C++ 类 `MyCppClass`，它有一个成员函数 `void doSomething()`：

```cpp
#include <iostream>

class MyCppClass {
public:
    void doSomething() {
        std::cout << "Doing something in C++" << std::endl;
    }
};
```

**2. 编写 JNI 头文件**

编写 JNI 头文件 `MyCppClassJNI.h`，声明 JNI 接口函数：

```cpp
/* MyCppClassJNI.h */
#include <jni.h>

#ifdef __cplusplus
extern "C" {
#endif

JNIEXPORT void JNICALL Java_com_example_MyJavaClass_doSomething(JNIEnv *, jobject);

#ifdef __cplusplus
}
#endif
```

**3. 编写 JNI 实现文件**

实现 JNI 接口函数 `Java_com_example_MyJavaClass_doSomething`，在该函数中创建 `MyCppClass` 对象并调用它的成员函数：

```cpp
/* MyCppClassJNI.cpp */
#include "MyCppClass.h"  // 包含你的 C++ 类的头文件
#include "MyCppClassJNI.h"

JNIEXPORT void JNICALL Java_com_example_MyJavaClass_doSomething(JNIEnv *, jobject) {
    MyCppClass myCppClass;
    myCppClass.doSomething();
}
```

**4. 生成动态链接库**

使用 C++ 编译器生成动态链接库（Windows 下为 DLL 文件，Linux 下为 SO 文件）：

- Windows 下可以使用 MinGW 或 Visual Studio 编译器。
- Linux 下可以使用 GCC 编译器。

以使用 MinGW 编译为例，在命令行中执行：

```sh
g++ -shared -o libMyCppClass.dll MyCppClass.cpp MyCppClassJNI.cpp -I"path/to/java/include" -I"path/to/java/include/win32"
```

**5. 编写 Java 类**

在 Java 中声明本地方法，并加载动态链接库：

```java
/* MyJavaClass.java */
public class MyJavaClass {
    // 声明本地方法
    public native void doSomething();

    // 加载动态链接库
    static {
        System.loadLibrary("MyCppClass"); // 加载 libMyCppClass.dll (Windows) 或 libMyCppClass.so (Linux)
    }

    public static void main(String[] args) {
        MyJavaClass obj = new MyJavaClass();
        obj.doSomething(); // 调用本地方法
    }
}
```

**6. 编译 Java 代码**

```sh
javac MyJavaClass.java
```

**7. 运行 Java 程序**

```sh
java MyJavaClass
```

这样，Java 程序就会调用本地方法 `doSomething()`，并执行 C++ 类 `MyCppClass` 的 `doSomething()` 函数。

### 使用 Java Native Access（JNA）

除了 JNI，你还可以使用 Java Native Access（JNA）库来简化与本地库的交互。JNA 允许 Java 应用程序直接调用本地库中的函数，而不需要编写 JNI 头文件和实现。

JNA 使用示例可以参考它的官方文档和示例代码：[JNA GitHub](https://github.com/java-native-access/jna)

总之，使用 JNI 或 JNA 可以将现有的 C++ 代码封装为 Java 中可调用的本地方法，从而在 Java 程序中实现对 C++ 功能的访问和集成。