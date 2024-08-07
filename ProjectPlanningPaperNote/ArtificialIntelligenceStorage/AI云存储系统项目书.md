# 智能存储方案

## 需求分析

需要构建一个优美明了的客户端界面，使用一端维护，主要是桌面客户端，要求界面现代且功能直观，同时跨平台。

用户可以通过使用这个客户端传输和发送文件，达到和现代网盘类似的文件上传和下载结果，并且可以拖拽管理文件，给用户提供图形化的可拖拽的文件管理面板。

上传的文件需要有一定的压缩和热点管理，保有断点续传功能。

同时在客户端右侧提供一个类似生成式人工智能窗口，并且提供一些功能模块。这些功能模块是本项目的一大亮点，包含以下功能：

1.   提供用户一定的问答咨询，包含一些现有的生成式 AI 的基本用法，可以网络检索也可以获取本软件的说明书

2.   可以让用户选择聚焦到某一个文件夹中分析数据和后台指示，总结这个文件夹的内容

3.   文件格式聚焦功能

     (1)针对文本文件，可以提供错字检查功能、文本拓展功能、灵感提供功能、高效检索功能、文本翻译功能、文本润色功能、思维导图生成、笔记导出功能、ppt 导出功能...

     (2)针对图片文件，可以提供图片训练 AI 功能

     (3)针对录音文件，可以提供文本转化功能

     (4)针对pdf文件，可以 ORC 识别文件再润色返回

客户端还需要呈现好看的仪表盘，方便用户监视存储管理。

需要有一个设置窗口，可以设置软件的一些配置，例如压缩格式、AI 接口密钥管理、主题更换

## 技术选型

### 编程语言

采用 [C99/Cpp20](https://zh.cppreference.com/w/%E9%A6%96%E9%A1%B5) 以下标准编写基本代码逻辑，以及少量的 [Python3](https://docs.python.org/zh-cn/3/contents.html) 脚本语言。

### 编码标准

采用 [最佳实践 Google C++ 风格指南中文版](https://zh-google-styleguide.readthedocs.io/en/latest/google-cpp-styleguide/contents.html)/[最佳实践 Google C++ 风格指南英文版](https://google.github.io/styleguide/cppguide.html) 和 [最佳实践 Google Python 风格指南中文版](https://zh-google-styleguide.readthedocs.io/en/latest/google-python-styleguide/contents.html)/[最佳实践 Google Python 风格指南英文版](https://google.github.io/styleguide/pyguide.html)。

### 网络框架

采用 [POCO](https://docs.pocoproject.org/current/) 库。
