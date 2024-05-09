# 1.React 组件

`React` 应用程序是由 **组件(返回标签的 JS 函数)** 组成的。一个组件是 `UI` 的一部分，它拥有自己的逻辑和外观。组件可以小到一个按钮，也可以大到整个页面。

```react
// 制作 MyButton 组件
function MyButton() {
    return (
        <button>
            I'm a button
        </button>
    );
}
```

至此，您已经声明了 `MyButton` 组件，现在可以把它嵌套到另一个组件中。

```react
// MyApp.js
// 制作 MyButton 组件
function MyButton() {
    return (
        <button>
            I'm a button
        </button>
    );
}

// 使用 MyButton 组件
export default function MyApp() {
    return (
        <div>
            <h1>Welcome to my app</h1>
            <MyButton />
        </div>
    );
}
```

组件我们有了，如何渲染呢？这些组件本质还是 `JS` 代码/库，因此可以借助 `HTML + CSS + 浏览器` 进行渲染。

```html
<!-- index.html -->
<!-- 准备一个带有 dir 的页面 -->
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Document</title>
    </head>
    <body>
        <div id="root"></div>
    </body>
</html>
```

```cpp
// styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

h1 {
  margin-top: 0;
  font-size: 22px;
}

h2 {
  margin-top: 0;
  font-size: 20px;
}

h3 {
  margin-top: 0;
  font-size: 18px;
}

h4 {
  margin-top: 0;
  font-size: 16px;
}

h5 {
  margin-top: 0;
  font-size: 14px;
}

h6 {
  margin-top: 0;
  font-size: 12px;
}

code {
  font-size: 1.2em;
}

ul {
  padding-inline-start: 20px;
}


```

然后，再结合一些必要的 `react` 配置。

```react
// index.js
import React, { StrictMode } from "react"; // 先不管这个, 导入 React 模块用的
import { createRoot } from "react-dom/client"; // 先不管这个, 导入 React DOM 模块用的
import "./styles.css"; // 先不管, 反正会自动根据 css 的文件内容来生成对应样式

import MyApp from "./MyApp";

const root = createRoot(document.getElementById("root"));
root.render(
  <StrictMode>
    <MyApp />
  </StrictMode>
);
```

渲染效果如下。

![image-20240509160541981](./assets/image-20240509160541981.png)

>   补充：在 `JS` 中 `export` 关键字用于导出模块的成员（变量、函数、类等），使其可以在其他文件中被导入和使用。这样做可以实现模块化开发，将代码分割成多个文件，提高代码的可维护性和复用性。
>
>   假设有名为 `math.js` 的模块，其中定义了一些数学函数：
>
>   ```javascript
>   // math.js
>   export function add(a, b) {
>       return a + b;
>   }
>   
>   export function subtract(a, b) {
>       return a - b;
>   }
>   ```
>
>   在另一个文件 `main.js` 中则可以使用 `import` 关键字来导入这些函数，并在该文件中使用它们：
>
>   ```javascript
>   // main.js
>   import { add, subtract } from './math.js';
>   
>   console.log(add(5, 3)); // 输出：8
>   console.log(subtract(5, 3)); // 输出：2
>   ```
>
>   在这个例子中，我们将 `add` 和 `subtract` 函数从 `math.js` 模块中导出，并在 `main.js` 中通过 `import` 关键字导入并使用了它们。这样做可以让我们在不同的文件中组织代码，提高了代码的可维护性和可读性。另外，这种导出也被称为命名导出。
>
>   `export default` 则是 `JS` 中用于导出默认成员的语法。默认导出意味着一个模块只能有一个默认导出，而且在导入时不需要使用花括号 `{}` 包裹，可以直接指定一个名称。
>
>   ```javascript
>   // greetings.js
>   const greeting = "Hello, ";
>   export default greeting;
>   ```
>
>   在这个示例中，`greetings.js` 模块默认导出了一个名为 `greeting` 的字符串。
>
>   在另一个文件 `main.js` 中则可以使用 `import` 关键字来导入默认导出的成员，而不需要使用花括号 `{}` 包裹：
>
>   ```javascript
>   // main.js
>   import greeting from './greetings.js';
>   
>   console.log(greeting + 'world'); // 输出：Hello, world
>   ```
>
>   一个模块只能有一个默认导出，并且可以与其他命名导出并存。当一个模块同时存在默认导出和命名导出时，可以同时使用 `export default` 和 `export` 关键字。
>
>   更详细的内容可以 [前往 MDN 查看什么是 export 和 import](https://developer.mozilla.org/zh-CN/docs/web/javascript/reference/statements/export)。

>   注意：`<MyButton />` 是以大写字母开头的，您可以据此识别 `React` 组件，`React` 组件必须以大写字母开头，而 `HTML` 标签则必须是小写字母。

>   吐槽：有点像 `HTML` 的标签/元素具有了类似函数的功能，而标签/元素的属性后面也可以作为函数的参数传递给组件...

上面所使用的标签语法被称为 `JSX`，它是可选的，但被大多数 `React` 项目所使用（主要是很方便）。需要注意的是 `JSX` 比 `HTML` 更加严格，必须使用闭合标签。