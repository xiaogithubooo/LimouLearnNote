# 1.json-cpp 的概要

`JSON` 是一种数据交换格式，常用于网络应用编程中的序列和反序列化（尤其是用于 `HTTP` 请求和响应的正文部分）。

`JSON` 的数据类型只有如下几种：

-   对象，使用 `{}` 包含
-   数组，使用 `[]` 包含
-   字符串，使用 `""` 包含
-   数字（包含整数和浮点），直接使用就行
-   布尔值，`true` 和 `false`
-   空值，`null`

而一份 `.json` 文件内就包含由上述数据类型构成的一份文档，当需要发送时就压缩和序列化成字符串发送出去，对端读取后再进行反序列进行阅读。

`json-cpp` 可用于实现 `json` 格式的序列化和反序列化，主要依靠三个类和少量函数。您可以使用 `sudo yum install jsoncpp-devel` 下载内部相关文件，也可以去克隆官方 `git` 仓库（https://github.com/open-source-parsers/jsoncpp）。

# 2.json-cpp 的使用

由于比较简单，这里直接上代码进行使用。

```cpp
//使用 jsoncpp 的相关接口
#include <iostream>
#include <string>
#include <sstream>
#include <memory>
#include <cstring>
#include <jsoncpp/json/json.h>

int main()
{
    //1.制作 JSON 内容
    std::cout << "1.制作 JSON 内容" << std::endl;
    //创建一个空的 JSON 对象
    Json::Value root;

    //添加键值对
    root["name"] = "John"; //实际上 operator[]() 重载返回的也是 Json::Value, 这里还重载了 operator=()
    root["age"] = 30;
    root["isStudent"] = true;
    root["email"] = Json::nullValue;

    //添加一个数组
    Json::Value hobbies(Json::arrayValue);
    hobbies.append("reading");
    hobbies.append("music");
    root["hobbies"] = hobbies;

    //添加一个嵌套的 JSON 对象
    Json::Value address;
    address["city"] = "New York";
    address["zipcode"] = "10001";
    root["address"] = address;

    
    //2.访问 JSON 对象
    std::cout << "2.访问 JSON 对象" << std::endl;
    //访问整个 JSON 对象
    std::cout << "JSON Object:" << root << std::endl;

    //访问 JSON 对象的值
    std::string name = root["name"].asString();
    int age = root["age"].asInt();
    bool isStudent = root["isStudent"].asBool();
    std::string email = root["email"].isNull() ? "null" : "no null";

    //访问数组元素
    std::cout << "hobby[]: ";
    for (const auto& hobby : root["hobbies"]) {
        std::cout << hobby.asString() << " ";
    }
    std::cout << std::endl;

    //访问内嵌 JSON 对象值
    std::string city = root["address"]["city"].asString();
    std::string zipcode = root["address"]["zipcode"].asString();

    //迭代 JSON 对象
    auto it = root.begin();
    auto key = it.key();
    auto value = (*it);
    std::cout << "类型: " << typeid(key).name() << " and " << typeid(value).name() << std::endl;
    
    for (auto it = root.begin(); it != root.end(); ++it)
    {
        auto key = it.key();
        auto value = (*it);
        std::cout << key << "----" << value << std::endl;
    }
    std::cout << std::endl;

    
    //3.序列化操作
    std::cout << "3.序列化操作" << std::endl;

    std::cout << "低版本:" << std::endl;
    //空白字符经过压缩的序列化
    Json::FastWriter aFastWriter;
    std::string jsonString = aFastWriter.write(root);
    std::cout << jsonString << std::endl;
    
    //带有缩进和换行的序列化
    Json::StyledWriter aStyledWriter;
    std::string jsonStringStyle = aStyledWriter.write(root);
    std::cout << jsonStringStyle << std::endl;

    //高版本的序列化
    //class StreamWriter
    //{
    //    int write(Value const& root, std::ostream * sou); //sou 是输出型参数
    //};

    //但是由于虚继承关系, 必须按照下面方式进行调用
    //using namespace Json
    //Value val = ...;
    //StreamWriterBuilder builder;
    //builder['...'] = ...
    //std::unique_ptr<StreamWriter> writer(builder.newStreamWriter());
    //writer->write(value, &std::cout); //这会输出到流中
    std::cout << "高版本:" << std::endl;
    Json::StreamWriterBuilder swb;
    std::unique_ptr<Json::StreamWriter> sw(swb.newStreamWriter());
    std::stringstream ss;
    sw->write(root, &ss);
    std::cout << ss.str() << std::endl;


    //4.反序列化操作
    std::cout << "4.反序列化操作" << std::endl;

    std::cout << "低版本" << std::endl;
    Json::Value newRoot_1;
    Json::Value newRoot_2;
    Json::Reader aReader;
    bool parsingSuccessful;
    parsingSuccessful = aReader.parse(jsonString, newRoot_1);
    parsingSuccessful = aReader.parse(jsonStringStyle, newRoot_2);

    std::cout << newRoot_1 << std::endl;
    std::cout << newRoot_2 << std::endl;

    //高版本的反序列化
    //class CharReader
    //{
    //  bool parse(char const* beginDoc, char const* endDoc, Value* root, std::string* errs); //root 输出型参数
    //};

    //但是由于虚继承关系, 必须按照类似 StreamWriterBuilder 的方式来使用 CharReaderBuilder
    std::cout << "高版本" << std::endl;
    Json::CharReaderBuilder crb;
    std::unique_ptr<Json::CharReader> cr(crb.newCharReader());

    Json::Value getRoot;
    const char* s = jsonString.c_str();
    std::string err;
    bool res = cr->parse(s, s + strlen(s), &getRoot, &err);
    std::cout << getRoot << std::endl;
    
    return 0;
}
```

不过如果是我的话，我会选择使用较高版本的接口，总结起来就是以下的接口。

```cpp
//常用高频接口
//制作 JSON 数据
Json::Value root; //创建 JSON 对象
root["key"] = value; //添加普通键值对或者嵌套 JSON 对象
Json::Value arr(Json::arrayValue); //添加数组对象
hobbies.append("arr[0]");
hobbies.append("arr[1]");
root["array"] = arr;


//序列化操作
Json::StreamWriterBuilder swb;
swb["indentation"] = ""; //设置缩进为空字符串, 实现压缩(还有一些其他选项您可以查阅以下, 这里不再展开)
std::unique_ptr<Json::StreamWriter> sw(swb.newStreamWriter());

std::stringstream ss;

sw->write(root, &ss); //写入流
std::cout << ss.str() << std::endl;


//反序列化操作
Json::CharReaderBuilder crb;
std::unique_ptr<Json::CharReader> cr(crb.newCharReader());

Json::Value getRoot;
const char* s = jsonString.c_str();
std::string err;

cr->parse(s, s + strlen(s), &getRoot, &err); //读取字符串
std::cout << getRoot << std::endl;
```