<style>
.heimu {
    position: relative; /* 使伪元素相对于其父元素定位 */
    display: inline-block; /* 使元素内容包裹 */
    color: transparent; /* 隐藏文字颜色 */
    text-decoration: none; /* 去掉链接下划线 */
}
.heimu::before {
    content: ''; /* 生成一个空内容伪元素 */
    position: absolute; /* 绝对定位 */
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: #F5F5F5; /* 白色遮盖 */
    z-index: 1; /* 确保覆盖文字 */
}
.heimu:hover::before {
    background-color: transparent; /* 鼠标悬浮时移除黑色遮盖 */
}
.heimu:hover {
    color: white !important; /* 鼠标悬浮时显示白色文字 */
    text-shadow: none; /* 去掉文本阴影 */
}
</style>

**如果本系列文章对您有帮助，可以 [star 一下我的 limou-learn-note](https://github.com/xiaogithubooo/LimouLearnNote)，求求惹(๑＞ ＜)☆♡~**

**叠甲：以下文章主要是依靠我的实际编码学习中总结出来的经验之谈，求逻辑自洽，不能百分百保证正确，有错误、未定义、不合适的内容请尽情指出！**

[TOC]

>   概要：主要是回答本章节内容是在做什么，为什么要这么做...

>   资料：先导需要搜集整个系列用到的所有资料，而每个章节的博文需要收集2本小节用到的所有资料...

---

>   吐槽：一些个人吐槽，可以看也可以不看... <span class="heimu" title="你知道的太多了">  这是一条黑幕 </span>

>   警告：一些容易错误、遗漏的点...

>   区别：一些容易混淆、混乱的点...

>   补充：一些补充性知识，最好是看一下...

# 1.

## 1.1.



## 1.2.

# 2.

## 2.1.

## 2.2.

# 3.

## 3.1.

## 3.2.

---

>   结语：...

**如果本系列文章对您有帮助，可以 [star 一下我的 limou-learn-note](https://github.com/xiaogithubooo/LimouLearnNote)，求求惹(๑＞ ＜)☆♡~**

