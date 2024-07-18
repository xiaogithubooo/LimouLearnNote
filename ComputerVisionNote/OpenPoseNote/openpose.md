```text
一、Ubuntu20TL 精简版安装 openpose 编译并运行使用
# 依赖安装，可以使用 -y 默认在下载的时候确认安装（protobuf、gflags、glog、opencv、libhdf5-dev、libatlas-base-dev）
sudo apt-get install libprotobuf-dev protobuf-compiler
sudo apt-get install libgflags-dev
sudo apt-get install libgoogle-glog-dev
sudo apt-get install libopencv-dev
sudo apt-get install libhdf5-dev
sudo apt install libatlas-base-dev
# 克隆 OpenPose 仓库并初始化子模块
git clone https://github.com/CMU-Perceptual-Computing-Lab/openpose
cd openpose/
git submodule update --init --recursive --remote
# 创建 build 目录使用 cmkae 构建 C++ 库
mkdir build && cd build && sudo cmake -DBUILD_PYTHON=ON -DGPU_MODE=CPU_ONLY ..
# makefile 构建，这里需要用到管理员权限，因为开启了 -j`nproc` 多任务加快构建，nproc 是系统可用核心数量，其实直接使用 make 也是可以的
sudo make -j`nproc`

二、Windows11 N卡 3090Ti GPU 加速便携版
1.windows文件夹下的依赖库文件
https://blog.csdn.net/weixin_44873541/article/details/134007808，下载完记得直接替换，否则Cmake不会自动寻找依赖库
2.修复模型文件问题
便携版脚本部署问题溯源：https://github.com/CMU-Perceptual-Computing-Lab/openpose/issues/1934
模型下载就解决方案（可能失效）：更全的模型文件下载地址，https://github.com/CMU-Perceptual-Computing-Lab/openpose/issues/2274（特典：修复不显示身体模型 https://github.com/CMU-Perceptual-Computing-Lab/openpose/issues/1204）
3.其他人关于运行便携版完整流程的一个示例例子（稍微参考一下就行，不是权威教程，这个教程也很一般...）
https://www.bilibili.com/video/BV138411q7B7/?vd_source=c92c89dbfcf9cc30c48086469621f35b
4.运行结果展示，确保有摄像头，然后在 cmd 下运行 bin\OpenPoseDemo.exe --face --hand 即可，后面两个参数可选，会找到脸和手
5.不同格式的数据也可以生成，例如 bin\OpenPoseDemo.exe --image_dir <源数据路径> --write_images <目的结果路径> --write_images_format  jpg --write_json <json存放路径>

三、Windows11 N卡 3090Ti GPU 加速下尝试编译构建
1.完整下载文档，https://github.com/CMU-Perceptual-Computing-Lab/openpose/blob/master/doc/installation/1_prerequisites.md 先解决一般
2.Windows11 本身的环境直接看文档，需要VS2022还有CMake、Python等模型，这部分主要看文档 https://github.com/CMU-Perceptual-Computing-Lab/openpose/blob/master/doc/installation/1_prerequisites.md
3.下载源代码，使用 git clone https://github.com/CMU-Perceptual-Computing-Lab/openpose.git
并且注意要拉取第三方库的代码，还有最好覆盖填入模型文件
4.CMake构建过程可以看看别人的博客 https://blog.csdn.net/SuiJiangPiaoLiu/article/details/126434521，也可以看上面的文档...
构建过程中如果出现找不 openpose.li 的问题，建议降级为 VS2017（解决思路来源https://blog.csdn.net/qq_44506213/article/details/115517420）VS2017的下载地址，https://blog.csdn.net/qq_29856169/article/details/115468970
并且最好在 CMakeList.txt 中的最后一行加上 add_compile_options(/wd1394) 以便禁用警告
5.在 VS2017 中进行构建后，注意杉树 CSDN 博客里的 CMake 使用的是 release 来生成的，所以记得切换编译模式来生成，再有什么错误就直接问 Chat-gpt 和 CSDN 和 github 的 issue 中查找吧...
运行示例中的几个代码程序即可...需要拿一个例子就设置为启动项
```

这玩意安装起来真坑...