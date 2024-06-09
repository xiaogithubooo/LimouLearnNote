**叠甲：以下文章主要是依靠我的实际编码学习中总结出来的经验之谈，求逻辑自洽，不能百分百保证正确，有错误、未定义、不合适的内容请尽情指出！**

[TOC]

>   前要：...

>   资源：这里有一个关于操作系统发展的视频，您可以观看一下 [操作系统究竟是怎么发展而来的，你真的了解吗？](https://www.bilibili.com/video/BV1Zc411D7sG?vd_source=4772b64d7a3cb1873f14bc0153c4de68) 这个视频。
>
>   在本系列文章中，有部分内容引用了 [阮一峰所撰写的 shell 教程](https://www.ruanyifeng.com/blog/2020/04/bash-tutorial.html)，您可以前往一看。
>
>   在系列的最末尾，我还参考了 [王道考研操作系统的视频](https://www.bilibili.com/video/BV1YE411D7nH?p=2&vd_source=c92c89dbfcf9cc30c48086469621f35b)，您也可以前去一看...旨在补充一些理论知识。

# 1.划时代的 OS

**操作系统（Operating System，OS）是控制和管理计算机系统硬件和软件资源的系统软件。**它合理地组织和调度计算机的工作和资源分配，为用户和其他软件提供方便的接口和环境，是计算机系统中最基本的系统软件。因此操作系统是最接近硬件的系统软件。

1.1.第一代 60 年代 Unix 操作系统
----------------

`UNIX` 是一种多用户、多任务的操作系统，最初由贝尔实验室的 `肯·汤普逊` 和 `丹尼斯·里奇` 在 `20` 世纪 `70` 年代早期开发。它被设计用于大型计算机系统，后来也衍生出许多类 `UNIX` 系统。`UNIX` 的特点：

1. 多用户和多任务：`UNIX` 支持多个用户同时访问系统，并且能够同时执行多个任务。每个用户可以拥有自己的账户，有独立的工作空间和权限控制（现在的很多的操作系统都支持这一特性，只不过大多数用户都没有“用户管理”的概念罢了）。

2. 强大而灵活的命令行界面：`UNIX` 采用了命令行界面，用户使用命令来操作系统。这种界面提供了强大的控制和灵活性，使得 `UNIX` 适用于各种任务和需求（早期的程序员都是在命令行环境下进行工作的）。

3. 层次化的文件系统：`UNIX` 使用层次化的文件系统，通过目录（`directory`）和子目录的组织，使得文件的管理更加方便。每个文件和目录都有一个唯一的路径，方便用户进行文件的定位和访问。

4. 网络功能：`UNIX` 具有强大的网络功能，支持网络通信和远程访问。它提供了网络协议堆栈和各种网络工具，如 `Telnet`、`FTP`、`SSH` 等，使得 `UNIX` 系统可以轻松地与其他计算机进行通信和共享资源。

5. 可移植性：`UNIX` 是高度可移植的操作系统，可以在多种硬件平台上运行。这意味着开发人员可以在不同的计算机系统上使用相同的代码和应用程序，提高了软件开发的效率和跨平台兼容性。

6. 开放源代码和标准化：`UNIX` 是基于开放源代码的操作系统，许多版本都是开放源代码的。此外，`UNIX` 也通过 `POSIX`（可移植操作系统接口）标准化了一些 `API` 和功能，增强了系统之间的互操作性。

7. 稳定性和安全性：`UNIX` 系统以其稳定性和安全性而闻名。它采用了严格的权限控制和访问控制机制，保护用户的数据和系统免受未经授权的访问和损坏。

由于 `UNIX` 的灵活性和可靠性，它被广泛用于大型服务器、工作站、超级计算机等环境，尤其适用于需要高度稳定性和可扩展性的场景。此外，许多现代操作系统，如 `Linux` 和 `MacOS`，都受到了 `UNIX` 的启发并继承了它的一些设计理念和特性。

## 1.2.第二代 80 年代 Windows 操作系统、MacOS 操作系统

### 1.2.1.Windows 操作系统

`Windows` 系统是由微软公司开发的操作系统系列，广泛应用于个人电脑和企业环境。它在全球范围内被广泛使用，并具有大量的用户和开发者基础。`Windows` 的特点：

1. 用户界面：`Windows` 系统采用了直观、易于使用的用户界面。它具有开始菜单、任务栏、桌面等经典元素，提供了丰富的可自定义选项，使用户能够按照自己的喜好来组织和访问应用程序、文件和设置。

2. 多任务管理：`Windows` 系统支持灵活的多任务管理。用户可以轻松切换和管理多个应用程序和窗口，通过任务视图、`[Alt+Tab]` 快捷键等功能实现高效的多任务操作。

3. 兼容性：`Windows` 系统具有广泛的硬件和软件兼容性，支持各种 PC 设备和外部设备的连接和使用。大多数软件和游戏都有 `Windows` 版本，使用户可以方便地进行工作、学习和娱乐。不过这点也蛮能被吐槽的，兼容性好到有的时候会卡出远古的 `Windows` 界面……

4. 应用程序生态系统：`Windows` 系统拥有庞大的应用程序生态系统。用户可以从 `Microsoft Store`、第三方应用商店和官方网站上下载各种应用程序、工具和游戏。这使得用户可以根据自己的需求和兴趣来扩展和定制系统功能。

5. 安全性：`Windows` 系统注重安全性，提供了多层次的安全防护措施。它有 `Windows Defender` 防病毒软件、实时防护、防火墙和安全更新等功能，帮助用户保护计算机免受恶意软件和网络威胁。

6. 网络和互联网功能：`Windows` 系统内置了各种网络和互联网功能，包括浏览器（如 Internet Explorer 和 `Microsoft Edge`）、电子邮件客户端、远程桌面连接等。这使得用户可以方便地上网浏览、发送电子邮件、进行远程访问等操作。

7. 支持与社区：`Windows` 系统得到了广泛的支持和社区参与。微软公司提供了丰富的文档、支持服务和在线社区，使用户能够获取帮助、解决问题和分享经验。

### 1.2.2.MacOS 操作系统

`MacOS` 是由苹果公司开发的操作系统，专为苹果电脑（`Mac`）设计。它提供了用户友好的界面、强大的功能和稳定的性能，成为了许多苹果用户的首选操作系统，`MacOS` 的特点如下：

1. 用户界面：`MacOS` 具有直观、美观和易于使用的用户界面。它采用了苹果独特的图标、菜单栏和任务栏，以及流畅的动画效果，确保用户的操作体验更加愉快。

2. `Spotlight` 搜索：`MacOS` 内置了强大的 `Spotlight` 搜索功能，可以快速查找文件、应用程序、联系人、日历事件等。用户只需要在搜索框中输入关键词，就能快速找到所需内容。

3. 多任务管理：`MacOS` 提供了灵活的多任务管理功能。用户可以通过 `Mission Control` 查看和切换不同的应用程序、窗口和虚拟桌面，使工作更加高效。

4. 内置应用程序：`MacOS` 内置了一系列实用的应用程序，如 `Safari` 浏览器、Mail 邮件客户端、`iTunes` 音乐播放器、`iMessage` 即时通信工具等。这些应用程序被精心设计，与操作系统紧密集成，提供优秀的用户体验。

5. `iCloud` 集成：`MacOS` 与苹果的云服务 `iCloud` 紧密集成。用户可以通过 `iCloud` 在不同设备上同步和共享文件、照片、联系人、日历等数据，方便实用。

6. 安全性：`MacOS` 注重安全性，在硬件和软件层面都提供了多种安全功能和保护机制。例如，`FileVault` 可以加密用户的硬盘数据，`Gatekeeper` 可以防止恶意软件的运行，内置的防火墙可以保护网络连接等。

7. 应用生态系统：`MacOS` 拥有丰富的应用程序生态系统。用户可以从 `Mac App Store` 下载各种应用程序，涵盖从生产工具到创作软件、娱乐应用等各个领域。

1.3.第三代 90 年代 Linux 操作系统
----------------

因为 `Linux` 是本系列文章的围绕点，因此 `Linux` 这一部分我们后面详细解读。

# 2.Linux 的认知

## 2.1.Linux 是什么

`Linux` 系统和 `Ubuntu` 系统、`Windows` 系统、`MacOS` 系统一样，都是一种操作系统。下面是 `Linux` 的官网：[The Linux Kernel Archives](https://www.kernel.org/)，您可以简单前去查看一下。

1. 简单讲：手敲指令交互（和 `Windows` 的图形化界面是不一样的）基于命令行的适合程序员的操作系统。

2. 复杂讲：`Linux` 是一种开源的、类 `Unix` 操作系统内核，广泛应用于各种设备和计算机系统中。它由芬兰计算机科学家 `Linus Torvalds` 在 `1991` 年开发，并得到了全球范围内的众多开发者的贡献和支持。`Linux` 的核心设计理念是以稳定、高性能和开放性为目标。具有许多优点，如良好的可移植性、可定制性、多用户支持等。作为一个开源操作系统，`Linux` 可以被任何人免费使用、修改和分发，这使得它成为了许多服务器、超级计算机、移动设备和嵌入式系统的首选操作系统。

2.2.Linux 的特点
----------------

1. 开源性：`Linux` 的源代码是开放的，任何人都可以查看、修改和重新分发。这使得用户可以自由地定制、优化和扩展 Linux 系统。
2. 多用户、多任务支持：`Linux` 支持多用户同时登录，并且可以同时执行多个任务。这对于服务器和大规模计算机集群非常重要。
3. 稳定性、安全性：`Linux` 内核经过了长时间的测试和改进，具有高度的稳定性和安全性。它可以长时间运行而不需要重启，并提供了各种安全功能来保护系统和用户数据。
4. 良好的可移植性：`Linux` 可以在各种不同的硬件平台上运行，从个人电脑到服务器、嵌入式设备和超级计算机等。
5. 丰富的软件支持：`Linux` 拥有庞大而活跃的开源社区，提供了丰富的软件和工具。用户可以轻松获取和安装各种应用程序、开发工具和服务。
6. 命令行界面：`Linux` 提供了强大的命令行界面，使用户可以通过命令来执行各种任务和管理系统。同时，也提供了图形用户界面（`GUI`）作为可选的交互方式。
7. 多发行版：`Linux` 有许多发行版（如 `Ubuntu`、`Red` `Hat`、`CentOS`、`Debian` 等），它们基于 `Linux` 内核，并以不同的配置和软件包集合来满足不同用户的需求。无论是作为服务器操作系统、个人电脑的替代品，还是嵌入式系统的基础，`Linux` 都广泛应用于各种领域，并成为计算领域中最重要的操作系统之一
8. ...

2.3.Linux 的用途
------------------

作为操作系统，本身的作用就是：**可以调动一台计算机的硬件资源，合理使用计算机资源，进而使得计算机发挥出更大的作用**。由于 `Linux` 本身是开源的，全世界的程序员都在一起维护，因此其稳定性和安全性深受企业喜爱，常用在：

1. 服务器后台
2. 嵌入式设备
3. 入网家用设备
4. 车载系统手机系统（安卓）
5. 云服务，例如：腾讯云、阿里云、亚马逊 `aws` 业务等这些企业都提供了 `Linux` 后台云服务
6. ...

# 3.Linux 的版本

## 3.1.从技术角度分类

主要是将 `kernel`（`Linux` 内核）版本做分类，“版本号组成 = 主版本.从/次版本.修正次数”。

<img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAASEAAADdCAYAAAD0OsTNAAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAAEnQAABJ0Ad5mH3gAAB7hSURBVHhe7Z3NTxvJ1sbff+TOgAgBbuKRh0GWRWCYgByCUHDkSYBrOWRkTcQIjYguCooQEYhwN2zYsPGKjTdsmA2bbLLJP3bec6o/6K6udrftstuxn8VvQVM0/XH6qVOfz//964cfCAAAsgIiBADIFIgQACBTIEIAgEyJEaFfaGblgAprR7RUv6FXBxc0YywHAADdESNCizRbu6bV3Wta//CVdj6mEaFFKr7/Rjufb2lhzvT7740JmvjtnNYOv1Ltf3JfX+lVfZfGjWXbZOIFFfevaM70u3bhcxXe3VL1M18jX2ft9JZW156by/5QpFy1SZtnTtnq4RXNPpowlAOgfyQ3x55fpxShCZpcu6TVNwc0OWb6/ffFON939bRJC4vL9KMcK17SJn+45ZeLxvJpmK1/oe1PLGpKMK4tiNALWmKR3KhUaHxCfp6myeUresXn3ygXtbITNFO9o52zW1oqTtO/xnL0uMY/n/J1TAXLdUjpmtaeG44DkIBFERoifjqh8uc7WgpmdLkDWju6o9XSL+GybfDj1CJN5hYp94Y/fhsiNHdBr/73hUqL4Wzm8ZsvtPPpinKBY//66ZwqLKJhcdqip8ecEf1ZDxzrDBE4iBDoBKsi9OPUc8oVl42/a4uJZSr8ZWiuPKpTqbofPtYD1Ef8/shO08uEPFMbIjT2hp4eGoRRzn/WoHzgmLqnz/w/tSxVZUeG4+mRZquTfUGEQCdYEKF9WlN9Jt+cvpO/TCKxRfPSv3TwhVafc9CWLqn8ye3DOG7SfOG+JlcfhZxH+0jn/nLKh86vPmb3OCMfwf3fMx1lcBV6evSNtt9uGX5nCVsiZGSCcn98oRo/p3sRXaT5A34eByc0ESrLcDNqh7OplSfa8TRozz/Iq+oLrfw0Ta1fUfn4q19G3v3SYrjZ+LgifZFNKh9xs/XQeX8/Fs9pw42X7d3gfbVJ/oBWP7r///NX2ty/oGK1Sdscu9tvvPe9S2uquWxg1FoEfcJqJjQjfQxGEVqm3NoFbZx9o/Jf11TZPaCZByw8Y4uc8XBQ6B/HWpNfuv6ROh9X5PxTu7TKTYpNP4h+oIlKk9bX9D6RlIwd0DoHnHxE44t8zd5H8/kLld/s0oSN/q5eidADzkRfN2nz4xXNhvp5OIOUzmjTu1FNum6zmBe09LH1OWa2ODZOm1Scy7nHpmnyRZNqn5tUeHhfbnJRRmUP6Ol7d0CkwO/g/Tk95nhR8cVZ26xbti0esricfqWNl/xe5R2qwQGJvXPKrV/S0oobL/L+uSn+VPrNvL+d4or2VGKicn8MWMOuCEkWYhQhwQnUVzXtRXKQbeofZMxHGnv+3xpU/XxDRfnwpri2e3eg1ZZuttYStw/I/SjX30jwX1D+JwnGCRqfO6INCUT9+juhByLkZYq142sq/hL4gBTO/W+art2931dbcSNqaUgWodl3fH1/Hzid/D5yXVrfm4t610c3tPY28C7HcjQxpd9bSuSZH2lxnDuhsv4e+HlsBCo0GVGccyvKyWA5YI2+i1AkUNVH0KUI/fALze5y+r53QvN/Nrob7XE/yh1pCmhZz/g6Z2hd9Z+49ECEFBNFmlq+oDKLZWUrKDjZi1AIEZNchfJrV1RpJUJexaL9riNMIvSQKxb9PTys0MxP9z+Pl29o54yzNVvXASIMiQgxU5xGc5Oj0tXHxLgf5fqaYf6MClpO1Qva8XbplQh5rDSo9r9bWvA/puybY04Htjvv6vNX2v50Sxu7N7TdSoRSxl0qIs2xZcr/caf1nWnIs+FrXXveYdMepGJ4RIjPs/HxjqqnDcp3k6kE+oSiv3cyirWSfrxNei1C7nXeZzc96pj2SRIhd47S51sqPXNFQB1PaI7ZFCG+hsf/uaHK4Rd/8ql0TOfiMpyxCi0dfqPqrqUJqiCWwRQh9WG0IUISMO+vWHyKVNj72mW/jXOdxv/zs1yr+aNpC1siNLVPq0e39PSJnrW5IhQQ0pZD9GcNmu2qiZkgQg8PaONzdD5TX0Vo/pK2U0+7cEXz+Ipywecyf0FLy4GfgRUGU4TUMW6Hhz6MorMsJHJ+CZjm/Yc4xU0mfaJhm0y+vlW19nygb0BQfUKn2iTATrAlQuo80lzQ5gktXnEz5yuVfg0c6+lkRcO7XTmnee8dqPdpeicBEXqQC3Va90SEWAg3di9pYW2fcj8v0oSM0JrKPuHnJzEUmDoicZZ726SlnwPlgBWSRUiGy2X2bYqaUg2h/jemthnjgD8y9LWo0TGtA3KMg/Mz1+S1rfv2+9sbKh/w+Tk4Nmr7brPCnSi3Fx51UbX+4QU9VksZOsC91p3jBs2FRse0/oGxOpVOJLVvZ73cNF8f3wc3gVZLpiUgE5Sv8/WzYFRem5qEAdTQsSzb8Jo40zRedEbxqizW4ffg1u6nN7Sglm0s8jOVn1kMQ8++k3v6gfJ/fuX/eeBMYXjAz+/vS3rs/f4hX+fZN9p+505xGMvR5OIRlf6+VcKwwhXIbI2bRoHzqVjSO5I1Jl/eqKaVnDcxw+H7ndu9o8oeN8k+faHtE3fqxdkdlbzheVWO7z8yHD9Nk88atCmZpH8M2CJGhKSG4hdkINpXElc2WPOFy/jnUDW5qTxnHSssLtKZyr+rfbqhFf5gZ7Y4gOrnVFipcNC5zSbtnOpD88/ZRdBMvaGFvcDCUJlY95smGm1+sP6ES41wdtiGCAmP6rS0/8Vdj8bXeXpLa6/fxHyULIAyj8h9rsYFrB2KkMzXKrkTAWufwhNQFYUjWgtMFHy1d065qcC9qtG88DsNYmrqpRchGT1l0V/RMx8W7WXJegJzj0IxqWG1jwp4JGdCAHz3yCzomFFNNRCBDCdLIEJgBHDnkX24pHzem7HNTexHFSpwhlTr5TpBkEhEhIxpKADfMSq2pQ/s9TVVTu+P107vqFy/33om+Det2Pz4T+ibAd0BEQJDjx7jcZj+1gREyC5ojgEAMgUiBADIFIgQACBTIEIAgEyBCAEAMiVGhOA7BgDoDzEiBN8xmcwG37ER51dZ0pFybRromOTmWOpV9PAdSwK+Y98Zsn6wfsUxgE3NeolFERoi4DvWNiJwQydCoC9YFSH4jqVErdS2IELwHestshd2cYtmAm4gwD4WRMjdpoODEL5jKbElQkbgOxYLN69C254c39D63jUtzIfLeTHklNMyYh3vnO49ST9b8WXD2YZmX5xC3P7V/TuqbL3geznxtzSR/rvQXkYjitVMCL5jKemVCMF3rAVyjYGN7lX/2SVVWmVwKhZaiZD0yXGsvK3TpPTJeRvFnTSosHxOK2XZI0lGmo9olZ/PJgvevY3UtNMnp2Wso4hdEZIaxChCghOo8B1jeiBC8B1LQDzGjgO7PXpw7GzE3nv89SnUs9O3IRbBj/6NvJ/anhaXiSI3GvRdhCKBql5klyIE3zEH+I614A2VgpY//vFpGo/ba7ojEdqilU9mETLvSgoRGhIRYqa4VuEmB3zHGPiOmREvetk7XOx+Dq6o6E2/iCVJJPi+Q82xHM1UbqhmuG6IUDzDI0J8HviOeTjXCd8xE9K0rtNCnQVQBlM+XMZ7j6UQifFn11ThuPM6u6uHDSrkoxUYRCiewRQh+I6lA75j3eEOjNRi50kliITcl3iTmX6nARGKZzBFSB2D71gi6jzwHUvF/D7Nae9TITPhTRmiIoUIcdOusndFSy8PKF9cpMmYjnOIUDzJIgTfMT4G37EkBt53TAT7+JoKLBR+c/CBrOHjzNnY9GZU09yUwXlM8zO9pc19mdfkLsnh65HpHBu/B7PxaSr8bRgYSDz/aBAjQqLQ/DANmNXcVDao8OEy/jlUTW4qD98xOQ7fsXtMTb22REjmGx00aXX//p3unLFYVKPPKhxDAbQsXOY+lV/rKwS4wsqfONm4cmvV7sk7h8oO74/HNmVHgORMCABg4LkaGTOOoqpZ92hmpQUiBECHSCZUO2lSsVj0uwNk/WS+ekNV6bD2mn2gJRER8tNGAEAszvcSXQ+3cyYd1dLUbP97mvz3rP8djhIQIQA6QP9u4jD9bRwQIQAAyACIEAAgUyBCAIBMgQgBADIFIgQAyJQYEYLvGACgP8SIEHzHZPo9fMcA6D3JzbHUq+jhO5YEfMcAiGJRhIYI+I61jQgcRAh0glURgu9YStTuARZECL5jYAiwIELuNh0chKrvxLipGXzHQtgSISPwHYuibTcjMRTaSiOY9e6qvaz8skFGrUXQJ6xmQvAdS0mvRAi+Yy0oUv4dx0/Q9ufRCa2bbHi4Kf5U+s28Y2rzOImJbrYNBnHYFSHJQowiJDiBCt8xpgci5GWK8B1rgWzYxu/Q6RcrUuFdgwp6pzw/j41AhSbl5tyKcjJYDlij7yIUCVT1EXQpQvAdc4DvWCLj3PysnjVpvtKgUnCrXo+HFZoJ7EU9Xr6hHS4fEStgjSERIWaK02hucsB3jIHvWAscR5adw/PkzEaeDV9raF9xYJ3hESE+D3zHPJzrhO+YCb7W93e0efqVSr+1mKgpNlKH36i6a2mCKohlMEUIvmPpgO9Ym4gY3ijxGV9rOk6pxvt2RVPfonX+gpaWAz8DKwymCKlj8B1LRJ0HvmOpKVzQut/p7MTTqy1DxvuEn5/EUMgxZIJyb5uugwawSbIIwXeMj8F3LImB9x17VKeVQ67YgjEhS3H04Xh3BC08HD9Nk88aXNZCxQEixIiQ1FAciAaifSVxZYM1X7iMfw5Vk5vKc9YB3zH4jgUwNfXSilAoJrzsSmVnhvOHYlLDevMQCMmZEAAA9BCIEAAgUyIiZExDAQA+mx//CX0zoDsgQgC0CUTILmiOAQAyBSIEAMgUiBAAIFMgQgCATIEIAQAyJUaE4DsGAOgPMSIE3zFnXdqw+Y652Pz/AHRJcnMs9Sp6+I4lka3vWC/+fwD4joEOsShCQ8Qw+o4x1v9/AFkkChECnWBVhOA7lhK1UtuCCLThOxb5vTURgu8Y6A4LIuRu08FBCN+xlFgVAR2T75iGrf+vPf8g0S1fsvQdc8kf0GpgO5HN/QsqVpu0zbG77W92Bt+xfmM1E4LvWEp6JUKxvmMa1v9/zK6ZATL3HXvI4iIbwL10N4BTnfMSe+eUW7+kpRU3XuA71nfsipBkIUYREpxAhe8Y0wMRau07ppGBCGXuOyb3rO/UmDuhsv4c+P3Dd6y/9F2EIoGqPvouRQi+Yw6xvmMaGYhQiCx8x0wipCyctOcA37G+MyQixEzBd8wn4jumkYkIZew7FmmOLVP+j7vWfWcSC/Ad6znDI0J8HviOeTjXGeuq2ncRcjfZ/5yl79gEPf7PDVUOv/iTT6VjOheX4cB3rG8MpgjBdywdbfiOhei3CA2C79j8JW2nnnYB37F+MpgipI7BdywRdZ6UvmNB+iFCg+Y7JiLEQrixe0kLa/uU+3mRJmSE1lQWvmN9JVmE4DvGx4bBd8wj4f8Pq+/Y2CLN7d5RZY+bZJ++0PaJO/Xi7I5K3vC8Ksf3D9+xvhIjQlJD8QsyEE3x48oGa75wGf8cqkY2lYfvmBy37TuW6v8Poe+YM3rKoruiZz4s2suS9QTmHoViUsNqHxXwSM6EAPjukVnQMaOaaiACGU6WQITACODOI/twSfm8N2Obm9iPKlTgDKnWy3WCIJGICBnTUAC+Y1Rsi/f+62uqnN4fr53eUbl+v/VM8G9aAcsfu0CEwNCjx3gcpr81ARGyC5pjAIBMgQgBADIFIgQAyBSIEAAgUyBCAIBMiREh+I4BAPpDjAjBd8xZlwbfsZHmV1nSkXJtGuiY5OaYrKVJJULwHUsCvmPfGbJ+sH7FMYBNzXqJRREaIuA71jaycHjoRAj0BasiBN+xlKiV2hZEAL5jvUX2wi5u0UzADQTYx4IIudt0cBDCdywlVkVAB75jsXDzKrTtyfENre9d08J8uJwXQ045LSPW8c7p3lP18IqKLxvONjT74hTi9q/u31Fl6wXfy4m/pYn034X2MhpRrGZC8B1LSa9ECL5jLZBrDGx0r/rPLqnSKoNTsdBKhKRPjmPlbZ0mpU9OFsm+5Ws8aVBh+ZxWyrJHkow0H9EqP59NFrx7G6lpeiz30ypjHRHsipDUIEYREpxAhe8Y0wMRgu9YAuIxdhzY7dGDY2cj1qEl/voUxm2I61Q6i/6NvJ/anhaXiSI3GvRdhCKBql5klyIE3zEH+I614A2VgpY//vFpGo/ba7ojEdqilU9mETLvSgoRGhIRYqa4VoHvmAN8x8yIF73sHS52PwdXVPSmX8SSJBJ836HmWI5mKjdUM1w3RCie4REhPg98xzyc64TvmAlpWtdpoc4CKIMpHy7jvcdSiMT4s2uqcNx5nd3VwwYV8tEKDCIUz2CKEHzH0gHfse5wB0Zqf9bNv08SCbkv8SYz/U4DIhTPYIqQOgbfsUTUeeA7lor5fZozNU9lJrw+OuuTQoS4aVfZu6KllweULy7SZEzHOUQonmQRgu8YH4PvWBID7zsmwnt8TQUWCr85+EDW0HHmHJcxqqa5KYPzmGaxvKXNfZnX5C6J4euR6Rwbvwez8Wkq/P2NNvUMPfH8o0GMCIlC88M0YFZzU9mgwofL+OdQNbKpPHzH5Dh8x+4xNfXaEiGZb3TQpNX9+3e6c8ZiUY0+q3AMBdCycJn7VH6trxDgCit/4mTjyq1VuyfvHCo7vD9uur9RITkTAgAYeK5GxoyjqGrWPZpZaYEIAdAhkgnVTppULBb97gBZP5mv3lBVOqy9Zh9oSUSE/LQRABCL871E18PtnElHtTQ12/+eJv8963+HowRECIAO0L+bOEx/GwdECAAAMgAiBADIFIgQACBTIEIAgEyBCAEAMiVGhOA7BgDoDzEiBN8xZ10afMcA6DXJzTFZ35VKhOA7lgR8xwCIYlGEhgj4jrWNLPqECIFOsCpC8B1Lido9wIIIwHcMDAEWRMjdpoODUPWdGDc1g+9YCKsioAPfsSjadjMSQ6GtNIJZ767ay8ovG2TUWgR9wmomBN+xlPRKhOA71oIi5d9x/ARtfx6d0LrJhoeb4k+LgR0S1eZxEhPdbBsM4rArQpKFGEVIcAIVvmNMD0TIyxThO9YC2bCN36HTaV+kwrsGFXSx5ve/EajQpNycW1FOBssBa/RdhCKBqj76LkUIvmMO8B1LZLx0TdWzJs1XGlQKbtXr8bBCM4G9qMfLN7TD5SNiBawxJCLETHEaDd8xB/iOtcBxZNk5PE/ObCQW+FpD+4oD6wyPCPF54Dvm4VwnfMdM8LW+v6PN069U+s20NauL2EgdfqPqrqUJqiCWwRQh+I6lA75jbSJieKPEZ3yt6TilGissVzT1LVrnL2hpOfAzsMJgipA6Bt+xRNR54DuWmsIFrfudzk48vdoyCPUTfn4SQyHHkAnKvW26DhrAJskiBN8xPgbfsSQG3nfsUZ1WDrliC8aELMXRh+PdEbTwcPw0TT5rcFmbwg08YkRIaigORAPRFD+ubLDmC5fxz6FqZFN5zjrgOwbfsQCmpl5aEQrFhJddqezMcP5QTGpYbx4CITkTAgCAHgIRAgBkSkSEjGkoAMBn8+M/oW8GdAdECIA2gQjZBc0xAECmQIQAAJkCEQIAZApECACQKRAhAECmxIgQfMcAAP0hRoTgO+asSxsy37FHdVrYu0u1xAOAfpHcHJO1NKlECL5jSWTqOyY2Rmd3tLL83FnsOpajmZdNqvI9GVeStwt8x0CHWBShIWIIfcdyb/mYYRuU2Xec6Ro8ytpFFolChEAnWBUh+I6lRK3UtiBCbfiOqRX0p9c0G3C2EJwV5t1cC3zHQHdYECF3mw4OQviOpcSWCBkx+46NPzmhUv0ospOg2ren00xXe/5Bolu+ZOk75pI/oNXAdiKb+xdUrDZpm2N329/sDL5j/cZqJgTfsZT0SoTS+o75OPv3lH/vNns17Kyokbnv2EMWF9kA7qW7AZwaHJDYO6fc+iUtrbjxAt+xvmNXhCQLMYqQ4AQqfMeYHohQW75jLmJno7KNroU1WYQy9x2TZ67v1Jg7obL+Hvj9w3esv/RdhCKBqj76LkUIvmMOaX3HBBb/isz/spHZpRChEFn4jplESFk4ae8BvmN9Z0hEiJmC75hPku8YC1D5v7YESEgjQhn7jkWaY8uU/+OutWe/xAJ8x3rO8IgQnwe+Yx7OdRp9x6xmQB5JIjQIvmMT9Pg/N1Q5/OJPPpWO6VxchgPfsb4xmCIE37F0tOs7FidAP3ETRBu6b48EERoE37H5S9pOPe0CvmP9ZDBFSB2D71gi6jwpfcdkhOeI/6eh5n/8pkFF2yI0aL5jIkIshBu7l7Swtk+5nxdpQkZoTWXhO9ZXkkUIvmN87Hv3HRMBv6PSyrKasR1k5lmDXkmN75UdVt+xsUWa272jyh43yT59oe0Td+rFmTwX7Z3Cd6yvxIiQ1FD8ggxE+0riygZrvnAZ/xyqJjeV56wDvmMWfcfi36ciOE9rCH3HnNFTFv0VPfNh0V6WrCcw9ygUkxpW+6iAR3ImBMB3j8yCjhnVVAMRyHCyBCIERgB3HtmHS8rnvRnb3MR+VKECZ0i1Xq4TBIlERMiYhgLwHaNie2yR8q+vqXJ6f7x2ekfl+v3WM8G/aQUsf+wCEQJDjx7jcZj+1gREyC5ojgEAMgUiBADIFIgQACBTIEIAgEyBCAEAMiVGhOA7BgDoDzEiBN8xZ10afMdGml9lSUfKtWmgY5KbY6lX0cN3LAn4jn1nyPrB+hXHADY16yUWRWiIgO9Y28jC4aETIdAXrIoQfMdSolZqWxAh+I71FtkLu7jV5YZvIAkLIuRuE8FBCN+xlNgSISPwHYuFm1ehbU+Ob2h975oW5sPlvBhyymkZsY53TveeqodXVHzZcLah2RenELd/df+OKtzs/bF44m9pIn1yob2MRhSrmRB8x1LSKxGC71gL5BoDG92r/rNLqrTK4FQstBIh6ZPjWHlbp0npk5NFsm/5Gk8aVFg+p5Wy7JEkI81HtMrPZ5MF795Gapoey/1oGesoYleEpAYxipDgBCp8x5geiBB8xxIQj7HjwG6PHhw7G7EOLfHXpzBuQ1yn0ln0b+T91Pa0uEwUudGg7yIUCVT1IrsUIfiOOcB3rAVvqBS0/PGPT9N43F7THYnQFq18MouQeVdSiNCQiBAzxbUKfMcc4DtmRrzoZe9wsfs5uKKiN/0iliSR4PsONcdyNFPh7NJw3RCheIZHhPg88B3zcK4TvmMmpGldp4U6C6AMpny4jPceSyES48+uqcJx53V2Vw8bVMhHKzCIUDyDKULwHUsHfMe6wx0Yqf1ZN/8+SSTkvoJOJS2ACMUzmCKkjsF3LBF1HviOpWJ+n+ZMzVOZCa+PzvqkECFu2lX2rmjp5QHli4s0GdNxDhGKJ1mE4DvGx+A7lsTA+46JYB9fU4GFwm8OPpA1fJw5G5vejGqamzI4j2kWy1va3Jd5Te6SHL4emc6x8XswG5+mwt/faFPP0BPPPxrEiJAoND9MA2Y1N5UNKny4jH8OVZObynPWAd8x+I4FMDX12hIhruw2Dpq0un//TnfOWCyq0QW84RgKoGXhMvep/FqfY8UVVv7EycaVW6t2T945VHZ4fzy2KTsCJGdCAAADz9XImHEUVc26RzMrLRAhADpEMqHaSZOKxaLfHSDrJ/PVG6pKE9dr9oGWRETITxsBALE430t0PdzOmXRUS1Oz/e9p8t+z/nc4SkCEAOgA/buJw/S3cUCEAAAgAyBCAIBMgQgBADIFIgQAyBSIEAAgU2JECL5jAID+ECNC8B1z1qXBdwyAXpPcHEu9ih6+Y0nAdwyAKBZFaIiA71jbyKJPiBDoBKsiBN+xlKjdAyyIEHzHwBBgQYTcbSI4CFXfSaS2FeA7FsKWCBmB71gUbSsTiaHQVhrBrHdX7WXllw0yai2CPmE1E4LvWEp6JULwHWtBkfLvOH6Ctj+PTmjdZMPDTfGnxcAOiWrzOImJbrYNBnHYFSHJQowiJDiBCt8xpgci5GWK8B1rgWzYxu/Q6bQvUuFdgwq6WPP73whUaFJuzq0oJ4PlgDX6LkKRQFUffZciBN8xB/iOJTJeuqbqWZPmKw0qBbfq9XhYoZnAXtQi1DtcPiJWwBpDIkLMFKfR8B1zgO9YCxxHlp3D8+TMRmKBrzW0rziwzvCIEJ8HvmMeznXCd8wEX+v7O9o8/Uql30xbs7qIjdThN6ruWpqgCmIZTBGC71g64DvWJiKGN0p8xteajlOqUYxd0dS3aJ2/oKXlwM/ACoMpQuoYfMcSUeeB71hqWITX/U5nJ56Ms8Wf8POTGAo5hkxQ7m3TddAANkkWIfiO8TH4jiUx8L5jj+q0csgVWzAmZCmOPhzvjqCFh+OnaZKf06YMSvjHgC1iREhqKA5EA9G+kriywZovXMY/h6rJTeU564DvGHzHApiaemlFKBQTXnalsjPD+UMxqWG9eQiE5EwIAAB6CEQIAJApEREypqEAAJ/Nj/+EvhnQHRAhANoEImQXNMcAAJkCEQIAZApECACQKRAhAECmQIQAAJkSI0LwHQMA9IMf6P8Bc4uAcYtwX8wAAAAASUVORK5CYII=" title="" alt="" data-align="inline">

一般来说从/次版本为偶数则是稳定的、可以直接使用的版本，而奇数则是测试版本。

修正次数则是指修正这个版本在运行中发现的问题的次数。

下面是 `Linux` 的内核版本网站：[Linux 内核版本网站](https://mirrors.edge.kernel.org/pub/linux/kernel/)

## 3.2.从商业角度分类

1. `Ubuntu`：由 `Canonical` 公司开发，是最为流行的 `Linux` 发行版之一，易于使用。

2. `Debian`：基于自由软件的社区开发的 `Linux` 发行版，稳定性和安全性得到广泛认可。

3. `Red Hat Enterprise Linux`：由 `Red Hat` 公司开发，主要用于企业级应用。

4. `Fedora`：由 `Red Hat` 公司推出，为开源社区提供实验性质的操作系统，新技术的试验平台。

5. `CentOS`：`CentOS` 是由社区维护的、基于 `Red Hat Enterprise Linux` 源代码构建而成的 `Linux` 发行版，适合用于服务器环境。（企业用的多）

6. `Arch Linux`：以简洁、灵活、轻量级著称，适合高级用户。

7. `openSUSE`：由 `SUSE Linux GmbH` 开发，提供易用、灵活、可扩展的操作系统。

8. `Gentoo`：由社区维护的源码型 `Linux` 发行版，极具个性化和定制性。

9. 除此之外还有很多其他的发行版，如 `Mageia`、`Puppy Linux`、`kail`、`红旗` 等。

# 4.Linux 的安装

## 4.1.云服务器与 Xshell（推荐）

购买一个年租的云服务器，再下载免费版的 `Xshell` 结合使用。部署简单（实在不会还能联系提供云服务器的厂商，协助安装，官方甚至还有写游戏），只是要钱...但是购买一个服务器可以多人使用噢！推荐腾讯云、华为云、阿里云（这些云服务器厂商有时有学生优惠）。

有了一台云服务器，您至少可以学习以下的知识模块：

- 原生态的学习一台 `Linux` 的使用，包含基本命令行操作、网络编程、操作系统概念...
- `MySQL、Redis` 等数据库的学习...
- 结合 `nginx` 和一个域名，可以在互联网上挂载自己的个人网站，不过这要求您至少会一些网络三大件的皮毛（`HTML`、`CSS`、`JS`），以及熟悉一些关于域名的备案流程
- 使用类似 `cpp-httplib、flask` 等框架搭建 `Web` 应用服务器
- 一个云服务器，到处可以运行，租用云服务器后，无论是个人电脑、手机、平板、机房...都可以使用一些 `ssh` 工具来访问服务器，真正意义上的便携（我以前懒得带一台电脑出门时，就经常会使用平板远端连接服务器作为我的替代方案）
- 维护简单，哪怕自己系统挂了，可以通过厂商的控制台一键还原操作系统，也可以直接找厂商修复还原

相信我，购买一个服务器并不贵（前提是性能不高），在学习过程中是很有性价比的。

<span style="text-emphasis:filled red;">关于如何购买云服务器并且进行连接，待补充...</span>

而本系列博文的所有操作都是基于云服务器上的，包括后续的网络内容，也在云服务器上进行展开。当然，也有一些非云服务器的后备方案，但是这些方案对新手不友好，而且可能涉及对网络学习的阻碍。不过我依旧给您稍微介绍一下，感兴趣的话您也可以鼓捣鼓捣...

>   强调：第一次买 `Linux` 服务器我推荐使用 `CentOs 7` 版本的`Linux` 服务器，这个版本的 `Linux` 最为经典也常用。而且对于云服务器来说，即便您现在选定了操作系统，未来也可以通过服务器厂商的可视化面板一键初始化系统，然后重新安装一些更为时髦的操作系统（我这个时候就流行使用 `Ubuntu`）。

## 4.2.虚拟机 VMware player（推荐）

成本最低，使用简单，可以在原有的某个操作系统上运行另外一个操作系统，不过偶尔会有奇怪的 `bug`，并且安装麻烦。但如果只是学习基本命令和一些系统调用基本也还过得去（就是网络编程可能会麻烦一点，包括以后要学习的 `Nginx`、`MySQL`、`Web` 等都有可能受到影响）。另外需要找到对应操作系统的镜像文件，不过这个也不难找，上操作系统的官网下录镜像文件就行，因此 `Linux` 纯新手比较推荐直接使用这个。 

<span style="text-emphasis:filled red;">待补充...</span>

## 4.3.直接安装 Linux 系统（推荐）

如果你有闲置的电脑，可以直接烧录一个用于重装系统的系统 `U` 盘保存系统文件，给电脑直接安装一个 `Linux` 系统（或者也可以搞一个双系统），好处就是最为“原生态”，但是对于丝毫不懂系统的新手来说配置的难度比较高。

并且这台电脑以后大概率是作为开发和工作使用了，基本不会在其他娱乐的方面有所作用（因为支持 `Linux` 的大众应用软件有可能不太好用，甚至没有对于的版本支持）。

## 4.4.Linux To Go 的使用（推荐）

去网店买一个系统 `U` 盘（不是普通 `U` 盘），这里的系统 `U` 盘是一种即插即用式的 `U` 盘，只要给自己的个人计算机插上 `U` 盘就可以使用某种操作系统，不过这种直接买的有时会偏贵（当然你也可以选择自己烧录一个……不过这对 U 盘的读取速度要求比较高，也可以换一种媒介）

我自己就有一个 `128G` 的 `Ubuntu` （一种 `Linux` 操作系统）系统 `U` 盘，使用起来还挺不错。

这个方案能进行原生态的使用一台 `Linux` 机器，并且文件能和原来的电脑一起共享，并且方便快捷，可以随身携带（我的大学的时候经常在 `U` 盘内部预先安装好各种软件和环境，到了机房就把 `U` 盘插到学校的计算机里哈哈），并且比双操作系统来得更快捷一些。

不过几个小问题：

1. 使用 `U` 盘的时候可能有点烫（这也许会把您吓倒）
2. 如果是自己烧录 `U` 盘可能需要一定的技术门槛
3. `U` 盘很容易坏，万一坏掉了，很容易整个系统都无法再访问了（包括里面的文件和数据），这点很危险，因此最好还是把 `U` 盘版的 `To Go`  技术当作是开发者便携的一个小工具

## 4.5.在 windows 中使用（不推荐）

在 `windows` 中可以使用类似 `Cygwin64Terminal` 这些软件模拟 `Linux` 的 `bash` 窗口，但是不太推荐。毕竟不够“原生态”，不利于您的学习，而且安装也是一个问题。

## 4.6.一些在线的命令行网站（不推荐）

新手可以提前寻找一些网站来使用 `Linux` 的系统：

1.   [Linux 在线网站 1](https://www.tutorialspoint.com/unix_terminal_online.php)
2.   [Linux 在线网站 2](https://www.tutorialspoint.com/unix_terminal_online.php)
3.   [Linux 在线网站 3](https://copy.sh/v86/?profile=linux26)。

虽然这样直接打开网站就可以使用了，但是我还是不太推荐：一是您也不知道这些网站的系统完整度如何，是否有一些许久都没有维护的 `bug`，二是有部分网站响应也比较缓慢，三是部分指令不支持，四是不利于除了 `Linux` 的其他模块的学习。

对于这些网站，我的看法是：您玩玩就好了，不过有的时候拿来测试一些比较危险的命令确实不错……

>   补充：另外如果您愿意，有的时候可以去一些比较奇怪的网站上探索一些更加古老的操作系统和学习机，这有助于我们了解一些操作系统的发展历程，譬如这个：[网页里的电脑博物馆云端的计算机博物馆，在网页模拟器中重现电脑发展史](https://www.compumuseum.com/index.html?from=bwg)。
>
>   您能体验到很多很多不同的操作系统，甚至可以在里面玩游戏和体验某些电脑病毒！
