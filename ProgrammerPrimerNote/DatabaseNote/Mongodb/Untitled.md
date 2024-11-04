https://www.mongodb.com/zh-cn/docs/manual/tutorial/install-mongodb-on-ubuntu/

`MongoDB` 和 `MySQL` 的用户管理虽然目标相同，但在实现上有一些关键差异，具体包括用户模型、权限管理机制和数据库设计方面。

1. 用户模型和作用范围

    `MySQL`：用户是全局性的，可以对不同数据库和表设置权限。每个用户都有全局和库级别的权限，支持细粒度控制，可以对单独的表、字段、存储过程等设置权限。

    `MongoDB`：用户是在特定的数据库中创建的。每个数据库都可以拥有独立的用户集合和权限，因此当连接到 MongoDB 时，需要明确指明身份认证的数据库。权限也可以跨数据库授权，但一般依赖于“角色”（例如 `userAdminAnyDatabase`）的授权。

2. 权限管理机制

    `MySQL`：采用“访问控制列表（`ACL`）”来管理权限。权限控制非常细致，可以授予用户对数据库、表、列甚至存储过程的特定操作权限，例如 `SELECT`、`INSERT`、`UPDATE` 等。

    `MongoDB`：`MongoDB` 使用角色进行权限控制，每个角色包含不同权限组合（即一系列预定义操作）。`MongoDB` 角色分为 `read`、readWrite、dbAdmin 等几种基本类型，还可以创建自定义角色。每个用户被赋予某些角色，角色定义了该用户可以在特定数据库上执行哪些操作。

3. 用户认证与连接机制

    `MySQL`：用户的认证方式通常基于用户名、密码以及主机地址（即用户只能从特定主机或 `IP` 地址连接）。`MySQL` 还支持多种身份验证插件，例如 `SHA256` 密码加密和 `LDAP`。

    `MongoDB`：支持基于用户名和密码的认证方式（类似 `MySQL`），但也支持多种认证机制，比如 `SCRAM`（默认）、`LDAP` 和 `Kerberos`。认证通常通过特定的身份验证数据库执行（默认是 `admin` 数据库）。

4. 用户和角色管理的灵活性

    `MySQL`：权限管理更细粒度，但通常需要为不同权限单独管理和分配，灵活但设置较繁琐。

    `MongoDB`：通过预定义角色进行快速授权，适合 NoSQL 数据库的高吞吐和快速开发要求；但细粒度控制不如 MySQL 精细。

5. 数据库初始配置

    `MySQL`：默认会在安装时初始化用户 root 并需要设置密码，安装后通常强制设置认证。

    `MongoDB`：安装时默认无用户，只有在开启认证后才会要求创建管理员账户。

在 MongoDB 中，有一些常用的指令和命令可以帮助用户进行数据库管理和数据操作。以下是一些常见的 MongoDB 指令及其用途：

### 1. 数据库相关命令

- **显示所有数据库**：
  ```javascript
  show dbs
  ```

- **使用某个数据库**：
  ```javascript
  use database_name
  ```

- **创建数据库**（在插入数据时自动创建）：
  ```javascript
  use new_database
  ```

- **删除数据库**：
  ```javascript
  db.dropDatabase()
  ```

### 2. 集合相关命令

- **显示当前数据库中的集合**：
  ```javascript
  show collections
  ```

- **创建集合**：
  ```javascript
  db.createCollection("collection_name")
  ```

- **删除集合**：
  ```javascript
  db.collection_name.drop()
  ```

### 3. 数据操作命令

- **插入文档**：
  ```javascript
  db.collection_name.insertOne({ key: "value" })  // 插入单个文档
  db.collection_name.insertMany([{ key1: "value1" }, { key2: "value2" }])  // 插入多个文档
  ```

- **查询文档**：
  ```javascript
  db.collection_name.find({ key: "value" })  // 查询所有匹配的文档
  db.collection_name.findOne({ key: "value" })  // 查询单个文档
  ```

- **更新文档**：
  ```javascript
  db.collection_name.updateOne({ query_key: "query_value" }, { $set: { update_key: "new_value" } })  // 更新单个文档
  db.collection_name.updateMany({ query_key: "query_value" }, { $set: { update_key: "new_value" } })  // 更新多个文档
  ```

- **删除文档**：
  ```javascript
  db.collection_name.deleteOne({ key: "value" })  // 删除单个文档
  db.collection_name.deleteMany({ key: "value" })  // 删除多个文档
  ```

### 4. 用户管理命令

- **创建用户**：
  ```javascript
  db.createUser({
      user: "username",
      pwd: "password",
      roles: [{ role: "readWrite", db: "database_name" }]
  })
  ```

- **删除用户**：
  ```javascript
  db.dropUser("username")
  ```

- **显示所有用户**：
  ```javascript
  db.getUsers()
  ```

### 5. 数据库权限和角色

- **查看当前用户的角色**：
  ```javascript
  db.runCommand({ connectionStatus: 1 })
  ```

- **查看指定用户的权限**：
  ```javascript
  db.getUser("username")
  ```

### 6. 其他常用命令

- **查看当前连接的数据库信息**：
  ```javascript
  db
  ```

- **获取数据库的状态信息**：
  ```javascript
  db.stats()
  ```

- **获取集合的状态信息**：
  ```javascript
  db.collection_name.stats()
  ```

这些是 MongoDB 中一些常见的指令，它们可以帮助你进行日常的数据库管理和数据操作。使用时请确保在相应的数据库和集合中执行命令。