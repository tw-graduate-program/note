# RESTful API Design

**Resource** REpresentational State Transfer 资源以某种表现形式进行状态转移

REST 风格的 Web API

1. 什么是 RESTful API

   用 **URL** 定位资源，**HTTP** 动词表示操作

2. 设计 RESTful API

   Identifier，Interaction，Representation

3. RESTful API 设计难点

   资源抽象是难点，必要时可以不使用 RESTful 风格

***

1. Resources & HTTP Verbs	——面向资源 URL，HTTP 动词表示操作

2. RESTful API 设计的优点：

   * 规范 API 设计
   * 增强 API 的可读性
   * 方便前后端的协作

3. URL 组成

   ![image-20221127181121551](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221127181121551.png)

   * 域名规范
   * 版本规范

4. 路径规范

   1. 路径中只能有名词(资源名),不能有动词，名词一般与数据库表名对应
   2. 资源名应该用复数形式
   3. 资源 ID 应该作为路径的一部分，紧跟在资源名后
   4. 对于多级资源，副资源应该在子资源之前
   5. 使用查询参数调整返回结果
   6. URL 统一使用小写字母
   7. URL 中使用字符 "-" 代替下划线 "_"
   8. URL 中不包含文件扩展名
   9. URL 中 "/" 表示层级关系，结尾不包含 "/"

5. HTTP Method

   ![image-20221127181849622](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221127181849622.png)

   ![image-20221127182046221](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221127182046221.png)

   ![image-20221127182833854](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221127182833854.png)

   ![image-20221127182904537](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221127182904537.png)

6. Request Body & Response Body

   数据格式：使用 JSON 格式

   ![image-20221127183439735](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221127183439735.png)

7. Request Body

   ![image-20221127183824488](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221127183824488.png)

8. Response Body

   ![image-20221127184031905](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221127184031905.png)

9. 请求失败时，返回结构化的错误信息，格式统一

10. 使用 RESTful API 的目的

    1. 看 URL 就知道要什么？	——资源 Resource
    2. 看 HTTP Method 就知道干什么？	——操作
    3. 看 HTTP Status Code 就知道结果？	——处理结果

11. RESTful API 设计难点

    如果不能满足 RESTful 风格，保证接口表达正确的意思即可

12. URL 表示资源 Resource，HTTP 动词表示操作

13. 设计 RESTful API：Identifier，Interaction，Representation

14. RESTful API 设计难点：资源抽象是难点，必要时可以不使用 RESTful 风格



### Practice：

GET /zoos

POST /zoos {"name":XXX}

GET /zoos/{id}

PUT /zoos/{id} {"name":XXX}

PATCH /zoos/{id} {"name":XXX}

DELETE /zoos/{id}

GET /zoos/{id}/animals

DELETE /zoos/{id}/animals/{id}