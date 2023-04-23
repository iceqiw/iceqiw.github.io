# Restful设计最佳实践
restful要设计得好并不容易，工作中见了太多错误不好的写法，所以我觉着自己可以再写一篇文章总结分享给大家。

## 使用版本号

/v1/books
/v2/books
不使用动词，用 Get / Post / Put / Patch / Delete 代表 获取 / 创建 / 修改(整个) / 修改(部分) / 删除

GET /v1/books 获取书列表
GET /v1/books/1 获取单本书
PUT /v1/books/1 修改整本书
PATCH /v1/books/1 修改书的部分信息
DELETE /v1/books/1 删除书
用复数

/v1/books
/v1/books/1
资源附属

GET /v1/users/1/books 获取id为1的用户的所有书
用减号(-)串连单词，如果用驼峰命名会使得url不美观

/v1/fiction-books
这些都是restful接口设计的基本原则，大多数情况下是管用的。但实际上这些基本原则并不能覆盖到所有场景。

所以我们需要对其做一些扩展。

长期的后端框架开发和总结分析我得出了下面几条restful扩展方法：

非增删改查资源操作，使用动词叠加在资源后表示一种资源操作目的

单数化，对确定只有一个结果的资源使用单数

代词，对确定的资源使用代词替换id数字

批量资源操作，使用逗号隔开的数字字符串代表多个资源

即时资源，不保存数据库即时创建即时返回资源

分类隔离，使用前缀分类接口



具体场景举例：

用户登录，注册，修改密码
使用非增删改查资源操作设计（推荐）

POST /v1/user/login
POST /v1/user/register
POST /v1/user/change-password
使用即时资源设计

POST /v1/token 登录，等同于创建一个token资源
使用基本原则设计

POST /v1/users 注册用户
PATCH /v1/user/password 修改密码

获取用户个人信息
单数化设计（推荐）

GET /v1/user 用单数user代表当前登录用户
GET /v1/user/books 当前登录用户的书
使用代词设计

GET /v1/users/me 保留restful基础的复数格式，用me代指自己，因为请求的时候id是存在header的token之中
GET /v1/users/me/books

生成pdf
使用非增删改查资源操作设计

POST /v1/books/{id}/generate-pdf 将id为1的书生成pdf文件

获取书的作者
假定系统规定，一本书只有一个作者，如果按照基本原则设计，需要做2个接口

调用方需要先调用列表接口拿到作者的id后，再次使用作者id获取作者信息

GET /v1/books/{id}/authors 获取作者列表，拿到author id
GET /v1/books/{id}/authors/{aid} 获取作者信息
为了简化调用，可以使用单数化设计，author改为单数代表这本书唯一的作者

GET /v1/books/1/author
附属单数化设计必须明确资源的对应关系是1对1


搜索
使用基本原则设计

GET /v1/books?title=西游记
如果搜索条件多了，使用url query的形式去设计就会出现限制，因为url的长度是有限的，此外url上还需要做一些字符转义的工作。

这个时候可以考虑，使用即时资源设计，改为POST请求

POST /v1/book-searches 即时创建searches资源返回给调用方

批量查询，删除
使用批量资源操作设计

GET /v1/books/1,2,3,5 获取id为 1,2,3,5 的4本书
DETELE /v1/books/1,2,3,5 删除id为 1,2,3,5 的4本书

内部接口，外部接口，测试接口....
微服务的接口不一定是全对客户端，部分接口可能是做微服务内部调用，也有可能做不同需求调用，这个时候可以在版本号前再加上前缀。

使用分类隔离设计

GET /internal/v1/users/1 内部接口获取用户信息
GET /v1/users/1 外部接口获取用户信息
POST /test/v1/users/1/change-password 测试接口修改密码


