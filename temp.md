# 1.网络诞生
那会人们刚刚整理好关于计算机和计算机之间的传输问题，明确了私网和公网的概念。

# 2.静态网站
这个阶段，人们知道服务器其实也是一台可以被公开访问的电脑，人们使用类似 `Nginx` 这种后端服务器程序，运行在特定 `IP` 和 `PORT` 上，管理众多的静态 `HTML` 页面，那会的应用就是静态网站，只能用来读取。这个阶段已经普及了浏览器，浏览器用户在 `URL` 中请求了什么页面，后端服务端程序就会根据请求返回服务器上对应路径存储的数据。

# 3.动态网站

人们意识到不一定用户访问什么资源，服务器上就一定需要存在对应资源，页面路由和请求接口可以发生解耦。

## 3.1.前后端不分离的服务端渲染（SSR）

1.   发送 **URL 请求** 用户在浏览器中访问一个页面，例如 `https://example.com/blog/post?id=123`
2.   **服务器端处理**
     -   服务器上的 `PHP` 脚本 `post.php` 接收 `id=123` 参数
     -   `PHP` 读取数据库中的内容（例如 `MySQL`）获取对应的文章数据
     -   `PHP` 脚本动态生成包含文章内容的 `HTML` 页面，将数据填充进 `HTML` 模板中
3.   **返回 HTML 页面**
     -   服务器将生成好的 `HTML` 返回给浏览器
     -   浏览器直接渲染 `HTML`，不需要额外的客户端渲染逻辑

##  3.2.前后端不分离的客户端渲染（CSR）
1. **初始页面请求** 用户第一次访问时，请求地址为：`https://example.com/news`服务器返回一个基础的 `HTML` 页面，其中包含页面结构和用于动态加载数据的 `JS` 代码，这些 `JS` 代码是内嵌到页面文件中的。

    ```php+HTML
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>News</title>
    </head>
    <body>
        <h1>Latest News</h1>
        <div id="news-list">
            <!-- 新闻内容将通过 JavaScript 动态加载 -->
        </div>
        <script>
            // 页面加载完成后通过 JavaScript 请求新闻内容
            document.addEventListener("DOMContentLoaded", function() {
                fetch('/api/get_news.php')
                    .then(response => response.json())
                    .then(data => {
                        const newsList = document.getElementById('news-list');
                        data.forEach(news => {
                            const newsItem = document.createElement('div');
                            newsItem.className = 'news-item';
                            newsItem.innerHTML = `<h2>${news.title}</h2><p>${news.content}</p>`;
                            newsList.appendChild(newsItem);
                        });
                    });
            });
        </script>
    </body>
    </html>
    ```

2. **动态数据获取** 在 `/api/get_news.php` 中，服务器端 `PHP` 脚本返回 `JSON` 格式的数据，但不渲染整个页面（有点想 `CGI` 程序）。

    ```php
    <?php
    // 连接数据库并获取新闻数据
    $mysqli = new mysqli("localhost", "username", "password", "news_db");
    $query = "SELECT title, content FROM news";
    $result = $mysqli->query($query);
    $news = [];
    while ($row = $result->fetch_assoc()) {
        $news[] = $row;
    }
    $mysqli->close();
    
    // 返回 JSON 数据
    header('Content-Type: application/json');
    echo json_encode($news);
    ```

>   补充：由此可见前后端是否分离和在那里渲染时无关的，并且我们这里说的渲染是指渲染为完整的可以交给浏览器的 `HTML` 页面文件，而不是指浏览器把 `HTML` 页面文件“渲染”到浏览器上的渲染...完整来说，前者被称为 `HTML` 渲染，后者被称为界面渲染。

# 4.前后解耦

实际上浏览器的 `URL` 切换时，即便不发送请求，也可以被 `JS` 检测到（此时我们的 `JS` 发展得非常强大，只使用 `JS` 也可以生成 `HTML` 和 `CSS`），因此我们可以根据这一特点，让 `JS` 前端代码和后端代码分离，单独部署到另外一个服务器上，这样就会有两个服务器，前端服务器和后端服务器，分别部署前端服务代码和后端服务代码，根据这一点结合两种渲染方式，就可以得到四种类别的应用：

-   前后端不分离 `SSR`
-   前后端不分离 `CSR`
-   前后端分离 `SSR`
-   前后端分离 `CSR`

前后端是否分离，抉择端的渲染，是权衡开发难度的

# 5.结合渲染

 

# 6.其他开发





---









---







在 C++ 中，计算 `std::vector<int>` 元素的和可以使用现代化的方法，如利用标准库中的算法。下面是一个使用 `std::accumulate` 的示例，它是最常用且简洁的方法。

### 使用 `std::accumulate`

```cpp
#include <iostream>
#include <vector>
#include <numeric> // for std::accumulate

int main() {
    std::vector<int> vec = {1, 2, 3, 4, 5};

    // 使用 std::accumulate 计算元素的和
    int sum = std::accumulate(vec.begin(), vec.end(), 0);

    std::cout << "The sum of the elements is: " << sum << std::endl;
    return 0;
}
```

### 代码解释

- `#include <numeric>`: 包含头文件以使用 `std::accumulate`。
- `std::accumulate`: 这个函数接收三个参数：
  - `vec.begin()`: 范围的开始迭代器。
  - `vec.end()`: 范围的结束迭代器。
  - `0`: 初始值，这里表示从 0 开始累加。

### C++ 17 及以后的方法

如果你使用的是 C++ 17 或更高版本，可以使用 `std::reduce`，它在并行算法的支持下可以提高性能。

```cpp
#include <iostream>
#include <vector>
#include <numeric> // for std::reduce
#include <execution> // for std::execution::par

int main() {
    std::vector<int> vec = {1, 2, 3, 4, 5};

    // 使用 std::reduce 计算元素的和（支持并行执行）
    int sum = std::reduce(std::execution::par, vec.begin(), vec.end(), 0);

    std::cout << "The sum of the elements is: " << sum << std::endl;
    return 0;
}
```

### 总结

使用 `std::accumulate` 和 `std::reduce` 是现代 C++ 中求解 `std::vector<int>` 元素和的推荐方法，既简洁又高效，且易于阅读。

`std::accumulate` 和 `std::reduce` 都是 C++ 标准库中用于进行聚合操作的算法，但它们有一些关键的区别和使用场景：

### `std::accumulate`

- **用途**：用于计算范围内所有元素的累加（或其他二元操作），通常用于求和。
- **头文件**：需要包含 `<numeric>`。
- **执行方式**：默认是串行执行。
- **初始值**：需要指定初始值（例如，从哪个值开始累加）。
- **灵活性**：可以自定义操作，例如通过传入一个自定义的二元操作符来进行不同的聚合计算。

**示例**：

```cpp
#include <iostream>
#include <vector>
#include <numeric> // for std::accumulate

int main() {
    std::vector<int> vec = {1, 2, 3, 4, 5};
    int sum = std::accumulate(vec.begin(), vec.end(), 0);
    std::cout << "Sum: " << sum << std::endl; // 输出 Sum: 15
    return 0;
}
```

### `std::reduce`

- **用途**：也用于计算范围内所有元素的累加（或其他二元操作），但支持并行执行，适合大数据集。
- **头文件**：需要包含 `<numeric>` 和 `<execution>`（如果使用并行执行）。
- **执行方式**：可以选择串行或并行执行（通过 `std::execution` 传递不同的策略）。
- **初始值**：同样需要指定初始值。
- **灵活性**：支持自定义操作和返回类型，适合更复杂的聚合。

**示例**：

```cpp
#include <iostream>
#include <vector>
#include <numeric> // for std::reduce
#include <execution> // for std::execution::par

int main() {
    std::vector<int> vec = {1, 2, 3, 4, 5};
    int sum = std::reduce(std::execution::par, vec.begin(), vec.end(), 0);
    std::cout << "Sum: " << sum << std::endl; // 输出 Sum: 15
    return 0;
}
```

### 总结

- **`std::accumulate`** 适合简单的、串行的聚合操作，如求和或计算乘积，通常用于较小或中等大小的容器。
- **`std::reduce`** 更适合处理较大数据集，尤其是在可以利用并行计算的情况下，它能提供更好的性能。

选择哪个取决于你的具体需求和数据集的大小。

`std::execution::par` 是 C++17 引入的一个执行策略，用于在并行模式下执行算法。它允许算法在多个线程中并行处理数据，以提高性能，尤其是在处理大数据集时。

### 主要特点

1. **并行执行**：
   - 使用 `std::execution::par` 可以让算法在多个线程中并行执行。例如，`std::reduce` 在这个模式下将使用多个线程来计算结果，从而加快处理速度。

2. **适用场景**：
   - 适合需要大量计算的操作，特别是当数据集较大且可以分割成独立的部分时。

3. **线程安全**：
   - 使用并行执行策略时，算法内部会自动处理线程安全，确保在并行计算过程中不会出现数据竞争。

4. **组合使用**：
   - 可以与其他标准算法一起使用，只需在调用算法时传入 `std::execution::par` 作为第一个参数。

### 示例

下面是一个使用 `std::execution::par` 进行并行计算的示例，计算 `std::vector<int>` 的元素和：

```cpp
#include <iostream>
#include <vector>
#include <numeric> // for std::reduce
#include <execution> // for std::execution::par

int main() {
    std::vector<int> vec(1'000'000); // 创建一个包含100万元素的vector
    std::iota(vec.begin(), vec.end(), 1); // 用1到100万填充vector

    // 使用并行执行策略计算元素的和
    int sum = std::reduce(std::execution::par, vec.begin(), vec.end(), 0);

    std::cout << "Sum: " << sum << std::endl; // 输出 Sum: 500000500000
    return 0;
}
```

### 注意事项

- **性能**：并行执行能显著提高性能，但并不总是适用于所有情况。对于小数据集，开销可能大于收益，导致性能下降。
- **编译器支持**：并行执行策略的支持取决于编译器和标准库的实现，确保使用的编译器支持 C++17 及其并行特性。

### 总结

`std::execution::par` 提供了一种简便的方式来利用多线程，提高在 C++ 中执行算法的效率，特别是在处理大规模数据时。

`std::iota` 是 C++ 标准库中的一个算法，用于将一个范围内的元素赋值为连续的递增值。这个算法通常用于初始化数组或容器，使其元素依次递增。

### 主要特点

1. **递增赋值**：
   - `std::iota` 从一个指定的初始值开始，依次为范围内的元素赋值，形成递增的序列。

2. **头文件**：
   - 需要包含 `<numeric>` 头文件。

3. **使用方式**：
   - 接受三个参数：起始位置的迭代器、结束位置的迭代器和初始值。

### 示例

下面是一个简单的示例，展示了如何使用 `std::iota`：

```cpp
#include <iostream>
#include <vector>
#include <numeric> // for std::iota

int main() {
    std::vector<int> vec(5); // 创建一个包含5个元素的vector

    // 使用 std::iota 初始化 vector
    std::iota(vec.begin(), vec.end(), 1); // 从1开始赋值

    // 输出结果
    for (int num : vec) {
        std::cout << num << " "; // 输出 1 2 3 4 5
    }
    std::cout << std::endl;

    return 0;
}
```

### 代码解释

- `std::vector<int> vec(5);`：创建一个包含 5 个元素的 `vector`，初始值为未定义。
- `std::iota(vec.begin(), vec.end(), 1);`：将 `vec` 中的元素依次赋值为 1, 2, 3, 4, 5。
- 输出结果为 `1 2 3 4 5`。

### 应用场景

- **初始化容器**：在需要生成一系列递增整数时，可以使用 `std::iota` 来快速初始化容器。
- **生成序列**：常用于生成连续的数值序列，方便进行后续计算。

### 总结

`std::iota` 是一个非常实用的算法，可以快速生成连续的递增数值序列，适用于各种初始化和数据生成的场景。



`std::minus<int>()` 是 C++ 标准库中定义的一个函数对象，用于执行整数的减法操作。如果你想自定义一个类似的功能，可以定义自己的函数对象（functor）或者使用 Lambda 表达式。以下是两种常见的方式。

### 1. 自定义函数对象

你可以通过定义一个类并重载 `operator()` 来创建一个自定义的减法函数对象。

```cpp
#include <iostream>
#include <vector>
#include <numeric>

class CustomMinus {
public:
    // 重载() 运算符进行减法
    int operator()(int a, int b) const {
        return a - b;
    }
};

int main() {
    std::vector<int> vec = {10, 2, 3, 4}; // 示例向量

    // 使用自定义的减法函数对象
    int result = std::accumulate(vec.begin(), vec.end(), 0, CustomMinus());

    std::cout << "The result of subtraction is: " << result << std::endl; // 输出结果
    return 0;
}
```

### 2. 使用 Lambda 表达式

如果你不想定义一个单独的类，可以直接使用 Lambda 表达式来实现减法逻辑，这通常更简洁。

```cpp
#include <iostream>
#include <vector>
#include <numeric>

int main() {
    std::vector<int> vec = {10, 2, 3, 4}; // 示例向量

    // 使用 Lambda 表达式进行减法
    int result = std::accumulate(vec.begin(), vec.end(), 0, [](int a, int b) {
        return a - b; // Lambda 表达式返回 a - b
    });

    std::cout << "The result of subtraction is: " << result << std::endl; // 输出结果
    return 0;
}
```

### 代码解释

- **自定义函数对象**：
  - 创建一个类 `CustomMinus`，并重载 `operator()` 使其能够接受两个参数并返回它们的差值。
  - 在 `std::accumulate` 中使用 `CustomMinus()` 作为操作符。

- **Lambda 表达式**：
  - 使用 `[](int a, int b) { return a - b; }` 定义一个 Lambda 表达式，它接收两个参数并返回它们的差值。
  - 在 `std::accumulate` 中直接传入这个 Lambda 表达式。

### 总结

使用自定义的函数对象和 Lambda 表达式都可以轻松实现减法操作。根据具体情况选择合适的方式，Lambda 表达式通常更加简洁易用。