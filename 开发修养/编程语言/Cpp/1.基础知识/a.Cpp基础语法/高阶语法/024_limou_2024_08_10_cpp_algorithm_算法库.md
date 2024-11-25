# 1.谓词

## 1.1.谓词的概念

谓词是一个返回布尔值 (`true` 或 `false`) 的函数或函数对象（仿函数），它们通常在标准库的算法中使用，用于指定条件或规则，因此需要引入 `<algorithm>` 头文件才可以使用。根据其接受的参数数量，谓词可以分为一元谓词和二元谓词。

## 1.2.谓词的使用

### 1.2.1.一元谓词

一元谓词是一个接受单个参数并返回布尔值的函数或函数对象。一元谓词通常用于需要根据某个条件筛选元素或判断元素是否满足某种条件的算法中，如 `std::find_if`、`std::count_if`、`std::remove_if` 等。

```cpp
// 使用一元谓词
#include <iostream>
#include <algorithm>
#include <vector>

// 一元谓词: 检查元素是否为正数
bool isPositive(int x) {
    return x > 0;
}

int main() {
    std::vector<int> numbers = { -10, 0, 5, -3, 8 };
    int count = std::count_if(numbers.begin(), numbers.end(), isPositive); // 使用 std::count_if() 和一元谓词来计算正数的数量
    std::cout << "正数的数量: " << count << std::endl;

    return 0;
}
```

在这个示例中，`isPositive` 就是是一个一元谓词，它检查一个整数是否为正数。`std::count_if()` 使用这个谓词来计算 `numbers` 容器中满足条件的元素数量。

### 1.2.2.二元谓词

二元谓词是一个接受两个参数并返回布尔值的函数或函数对象，二元谓词常用于需要比较两个元素或在两个元素之间建立关系的算法中，如 `std::sort`、`std::max_element`、`std::equal` 等。

```cpp
// 使用二元谓词
#include <iostream>
#include <algorithm>
#include <vector>

// 二元谓词: 检查第一个元素是否小于第二个元素
bool isLessThan(int x, int y) {
    return x < y;
}

int main() {
    std::vector<int> numbers = {5, 3, 8, 1, 2};
    std::sort(numbers.begin(), numbers.end(), isLessThan); // 使用 std::sort() 和二元谓词来对向量进行排序
    std::cout << "排序后的向量: ";
    for (int n : numbers) {
        std::cout << n << " ";
    }
    std::cout << std::endl;

    return 0;
}
```

在这个示例中，`isLessThan` 是一个二元谓词，它检查第一个参数是否小于第二个参数。`std::sort` 使用这个谓词来对 `numbers` 容器中的元素进行排序。

这些谓词在 `Cpp` 标准库的算法中广泛使用，帮助开发者根据特定条件进行元素筛选、排序、比较等操作。