>   前要：关于 `MySQL` 的基础命令行使用，其相关的学习资料我在前面已经指出，这里我不再提及数据库安装和数据库操作的问题，默认您看到这里时，已经看过我给的 `MySQL` 资料并且对数据库有一定的操作实践了...当然，您可以尝试看下去，因为本篇对于 `MySQL` 的原生命令行操作比较少，而是使用 `ORM` 框架来操作数据库。

# 1.Flask 连接数据库

连接数据库就需要安装有关 `Python` 操作 `MySQL` 的数据库驱动模块，这里我推荐两款模块（`2024-3-27`）：

-   `MySQL` 官方 `mysql-connector-python`，有专门的团队进行更新，如果需要使用最新的 `MySQL` 功能，可以选择
-   纯 `Python` 编写的 `pymysql`，虽然效率稍微低了些，但是基本是无缝衔接 `Python` 的编码过程
-   纯 `C` 编写的 `mysqlclient`，执行效率高，但却是第三方模块，在 `Linux` 下部署会相对容易

>   注意：`MySQL-python` 已被 `python3` 淘汰，`python3` 已不支持使用该模块，但是该模块的另外一个分支模块 `mysqlclient`，是由以往的习惯使用 `MySQL-python` 的开发者维护的第三方模块, `python3` 支持使用该模块（该模块是纯 `C` 项目，因此执行效率比较高，但是安装很容易出错，这点不太友好）。

综合考虑，我决定使用 `pymysql` 较为稳妥（单纯只是易安装，并且对于小项目来说也够用了）。当然，您完全可以在您的程序中封装一个软件层，主程序中使用这个封装的接口，封装内部调用驱动模块的函数。这样就实现了软件设计上的一种解耦，后续替换驱动模块也会比较容易。

使用下面的命令行指令安装 `pymysql` 驱动模块：

```python
pip install pymysql
```

# 2.ORM 技术简化 SQL 操作

**ORM(Object Relational Mapping)，即“对象关系映射”**，这是一种常见的编程技术，它主要用来在面向对象的编程语言和关系数据库之间建立一座桥梁。

其核心功能是将编程语言中的 **类和对象** 与数据库中的 **表和记录** 之间建立起一种 **映射关系**。程序员可以用面向对象的方式来操作数据库，而无需编写复杂的 `SQL` 语句。可以简化数据库操作，提高开发效率，让代码更易于维护。

`ORM` 框架通常会提供一些基本的功能，包括但不限于：

- **类的映射**：将程序中的数据模型（通常是类）映射到数据库的表结构。
- **可持久化**：将内存中的对象保存到数据库中，或者从数据库中读取数据并转换为对象。
- **对象抽象**：允许使用面向对象的方式构建数据库查询，而不是直接编写 `SQL` 语句，可以很快更换不同的数据库，而无需修改对数据库的操作。
- **事务管理**：提供事务控制的功能，确保数据的一致性和完整性。
- **安全性高**：无需担心 `SQL` 注入的问题，这方面具有安全性，减低开发成本

在 `Flask` 中，一般不会直接使用原生的 `SQL` 语句，我也不推荐您这么做，更多是使用 `sqlalchemy` 中提供的 `ORM` 技术，可以和使用 `python` 对象一样实现对数据库的增删查改。

而 `Flask` 的内部又把 `sqlalchemy` 封装为 `flask-sqlalchemy`，而由于不是所有 `Flask` 都需要数据库，因此这个模块需要您自己安装。

```python
pip install flask-sqlalchemy
```

>   补充：由于 `flask-sqlalchemy` 是 `sqlalchemy` 的封装，因此安装的同时也会安装 `sqlalchemy`。

## 2.1.连接数据库

我们先来尝试连接一下，至于具体的操作细节我在代码中有给出注释，您简单看一下即可：

```python
from flask import Flask, render_template
from flask_sqlalchemy import SQLAlchemy
from sqlalchemy import text
import os

# 通过当前文件名创建 Flask 对象
app = Flask(__name__)

# 配置 app.config 内连接数据库的信息
app.config['SQLALCHEMY_DATABASE_URI'] = (
    f"mysql+pymysql://"
    f"{os.getenv('EIMOU_DATABASE_USER')}:{os.getenv('EIMOU_DATABASE_PASSWORD')}@"
    f"{os.getenv('EIMOU_DATABASE_HOST')}:3306/{os.getenv('EIMOU_DATABASE_NAME')}"
    "?charset=utf8mb4"
)

# 通过 Flask 对象创建 SQLAlchemy 对象
db = SQLAlchemy(app)

# 对 SQLAlchemy 对象进行 SQL 操作
with app.app_context(): # 第一个 with 语句结束时，应用上下文将被销毁，这意味着应用程序的状态将恢复到原始状态
    with db.engine.connect() as conn: # 第二个 with 语句则用于确保在执行完数据库操作后，连接对象 conn 会被正确关闭和释放，避免资源泄漏。其中 db.engine.connect() 和数据库建立了连接
        rs = conn.execute(text("select 1+1")) # conn.execute() 用于执行数据库操作，它接受一个 SQL 语句作为参数，而 text() 可以将字符串转换为 SQL 表达式对象
        print(rs.fetchone()) # 由于 rs 是一个查询结果对象，它包含了执行 SQL 查询后返回的结果集，使用 fetchone() 就可以从结果集中获取一行数据

# 路由和视图函数的定义
@app.route('/')
def index():
    return 'I am a test...'

if __name__ == '__main__':
    app.run(debug=True) # 启动 Web 后端服务
```

这里提一嘴，在使用 `Flask-SQLAlchemy` 进行数据库操作时，通常情况下是需要使用应用上下文（`Application Context`）的，这是因为 `Flask-SQLAlchemy` 依赖于 `Flask` 的应用上下文来管理数据库连接和事务。

## 2.2.操作数据表

首先您需要明白，具有可以映射到数据表能力的 `python` 类我们称为一个 `ORM` 模型，一个 `ORM` 模型和一个数据库中的表进行对应，模型的类属性分别对应表的每一个字段，模型的每个实例对象对应表中的记录。

>   吐槽：而实际上，所谓的 `ORM` 模型，就是一个继承过来的子类，每个子类就是对一张数据表的描述。

```mermaid
flowchart TD
sql["MySQL"] --> database["Database"] --> table_1["table_1"] & table_2["table_2"] & table_3["table_3"] & table_n["..."]
table_1 -->|"映射"| orm_1["ORM_1"] -->|"实例化"| record_1["record_1"] & record_2["record_2"] & record_n["..."]
```

我们来尝试在内存中创建一个“数据表/`ORM` 模型”，并且和数据库上的数据表进行同步（没有对应的数据表就进行创建），然后进行 `SQL` 语句中常用的 `CRUD(增删查改)` 操作。



