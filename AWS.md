# AWS

### SNS

Amazon Simple Notification Service (Amazon SNS) 是一项托管服务，提供**从发布者向订阅者**（也称为*创建者*和*使用者*）的 **消息传输**。发布者通过将消息发送至**主题**与订阅者进行异步交流，主题是一个逻辑访问点和通信渠道。客户端可以订阅 SNS 主题并使用受支持的终端节点类型接收已发布的消息，例如 Amazon Kinesis Data Firehose、Amazon SQS、AWS Lambda、HTTP、电子邮件、移动推送通知和移动短信 (SMS)。

* 消息传输
* 发布者
* 订阅者
* 主题

![image-20221222105754089](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221222105754089.png)



扇出到 SQS 队列

Amazon SNS 与 Amazon Simple Queue Service (Amazon SQS) 密切配合。Amazon SNS 允许应用程序通过“推送”机制将时间要求严格的消息发送到多个订阅者，无需定期检查或“轮询”更新。Amazon SQS 是分布式应用程序通过轮询模型交换消息的消息队列服务，它可用于解耦发送和接收组件，而无需每个组件同时可用。通过将 Amazon SNS 和 Amazon SQS 配合使用，可以将消息发送到要求立即通知事件的应用程序，也可以在 Amazon SQS 队列中存留消息以供其他应用程序稍后进行处理。

* 将消息发送到要求立即通知事件的应用程序
* 在 SQS 队列中存留消息以供其他应用程序进行处理



扇出到 Lambda 函数

Amazon SNS 和 AWS Lambda 集成在一起，以便您可以使用 Amazon SNS 通知调用 Lambda 函数。当消息发布到SNS 主题时 Lambda函数订阅去订阅它 ，已发布消息的 负载均衡的调用 Lambda 函数。Lambda 函数接收消息负载作为输入参数，可以操作消息中的信息、将消息发布到其他 SNS 主题或向其他 AWS 服务发送消息。

* 使用 Amazon SNS 通知调用 Lambda 函数
* 当消息发布到SNS 主题时 Lambda函数订阅去订阅它
* 以操作消息中的信息、将消息发布到其他 SNS 主题或向其他 AWS 服务发送消息



步骤

1. 创建主题 topic
2. 创建主题订阅
3. 向主题发送消息



```json
# An Amazon SNS topic with two Amazon SQS queue subscriptions
"MySNSTopic" : {
  "Type" : "AWS::SNS::Topic",
  "Properties" : {
    "Subscription" : [{
      "Endpoint" : { "Fn::GetAtt" : [ "MyQueue1", "Arn" ] },
      "Protocol" : "sqs"
    }, {
      "Endpoint" : { "Fn::GetAtt" : [ "MyQueue2", "Arn" ] },
      "Protocol" : "sqs"
    }],
    "TopicName" : "SampleTopic"
  }
}
```

```json
# Create SNS by CloudFormation Designer
{
    "Resources": {
        "SNST3WZGW": {
            "Type": "AWS::SNS::Topic",
            "Properties": {},
            "Metadata": {
                "AWS::CloudFormation::Designer": {
                    "id": "16e9627d-cdbc-4ed5-8a42-2c5d0c18aaac"
                }
            }
        }
    }
}
```

```json
# Create a subscription with mandatory attributes
"MySubscription" : {
  "Type" : "AWS::SNS::Subscription",
  "Properties" : {
    "Endpoint" : "710116572@qq.com",
    "Protocol" : "email",
    "TopicArn" : { "Ref" : "MySNSTopic" }
  }
}
```





### SQS

Amazon Simple Queue Service (Amazon SQS)提供了一个安全、持久且可用的托管队列，以允许您集成和解耦分布式软件系统和组件。Amazon SQS 提供常见的结构，例如[死信队列](https://docs.aws.amazon.com/zh_cn/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html)和[成本分配标签](https://docs.aws.amazon.com/zh_cn/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-queue-tags.html). 

**队列类型**

![image-20221227112846739](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221227112846739.png)

步骤：

1. 创建队列
2. 发送消息
3. 接收消息



```json
# Create SQS queue
{
    "Resources": {
        "SQSQ51310": {
            "Type": "AWS::SQS::Queue",
            "Properties": {},
            "Metadata": {
                "AWS::CloudFormation::Designer": {
                    "id": "9df4c69f-5f89-4e7b-9e07-c4fa8e2ef3d3"
                }
            }
        }
    }
}

```

```json
# Create an Amazon SQS queue and one or more CloudWatch alarms
{
  
  "Description" : "This template creates an Amazon SQS queue and one or more CloudWatch alarms. ",
 
  "Parameters" : {
    "AlarmEmail": {
      "Default": "710116572@qq.com",
      "Description": "Email address",
      "Type": "String"
    }
  },
 
  "Resources" : {
    "MyQueue" : {
      "Type" : "AWS::SQS::Queue",
      "Properties" : {
         "QueueName" : "SampleQueue"
      }
    },
    "AlarmTopic": {
      "Type": "AWS::SNS::Topic",
      "Properties": {
        "Subscription": [{
          "Endpoint": { "Ref": "AlarmEmail" },
          "Protocol": "email"
        }]
      }
    },
    "QueueDepthAlarm": {
      "Type": "AWS::CloudWatch::Alarm",
      "Properties": {
        "AlarmDescription": "Alarm if queue depth increases to more than 10 messages",
        "Namespace": "AWS/SQS",
        "MetricName": "ApproximateNumberOfMessagesVisible",
        "Dimensions": [{
          "Name": "QueueName",
          "Value" : { "Fn::GetAtt" : ["MyQueue", "QueueName"] }
        }],
        "Statistic": "Sum",
        "Period": "300",
        "EvaluationPeriods": "1",
        "Threshold": "10",
        "ComparisonOperator": "GreaterThanThreshold",
        "AlarmActions": [{
          "Ref": "AlarmTopic"
        }],
        "InsufficientDataActions": [{
          "Ref": "AlarmTopic"
        }]
      }
    }
  },
  "Outputs" : {
    "QueueURL" : {
      "Description" : "URL of new Amazon SQS Queue",
      "Value" : { "Ref" : "MyQueue" }
    },
    "QueueARN" : {
      "Description" : "ARN of new Amazon SQS Queue",
      "Value" : { "Fn::GetAtt" : ["MyQueue", "Arn"]}
    },
    "QueueName" : {
      "Description" : "Name new Amazon SQS Queue",
      "Value" : { "Fn::GetAtt" : ["MyQueue", "QueueName"]}
    }
  }
}
```





### Lambda

Lambda 是一项计算服务，可使您无需预置或管理服务器即可运行代码。Lambda 在可用性高的计算基础设施上运行您的代码，执行计算资源的所有管理工作，其中包括服务器和操作系统维护、容量调配和弹性伸缩和记录。借助 Lambda，您可以为几乎任何类型的应用程序或后端服务运行代码。您只需要以 [Lambda 支持的一种语言](https://docs.aws.amazon.com/zh_cn/lambda/latest/dg/lambda-runtimes.html)提供您的代码。

您可以将代码组织到 [Lambda 函数](https://docs.aws.amazon.com/zh_cn/lambda/latest/dg/gettingstarted-concepts.html#gettingstarted-concepts-function)。只有在需要时 Lambda 才运行您的函数，并且能自动扩展，从每天几个请求扩展到每秒数千个请求。

![image-20221227114617742](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221227114617742.png)

步骤：

1. 创建 Lambda 函数
2. 调用函数(Test->New Event->Save->Test)

```json
# Create lambda function
{
    "Resources": {
        "LF4AGSX": {
            "Type": "AWS::Lambda::Function",
            "Properties": {},
            "Metadata": {
                "AWS::CloudFormation::Designer": {
                    "id": "55b12094-0687-4199-b2f1-74c02e2d39ab"
                }
            }
        }
    }
}
```

```json
{
      "Type": "AWS::Lambda::Function",
      "Properties": {
        "Handler": "index.handler",
        "Role": { "Fn::GetAtt" : ["LambdaExecutionRole", "Arn"] },
        "Code": {
          "ZipFile":  { "Fn::Join": ["", [
            "var response = require('cfn-response');",
            "exports.handler = function(event, context) {",
            "   var responseData = {Value: event.ResourceProperties.List};",
            "   responseData.Value.push(event.ResourceProperties.AppendedItem);",
            "   response.send(event, context, response.SUCCESS, responseData);",
            "};"
          ]]}
        },
        "Runtime": "nodejs8.10"
      }
    },
```



### S3

Amazon Simple Storage Service（Amazon S3）是一种对象存储服务，提供行业领先的可扩展性、数据可用性、安全性和性能。各种规模和行业的客户可以使用 Amazon S3 存储和保护任意数量的数据，用于数据湖、网站、移动应用程序、备份和恢复、归档、企业应用程序、IoT 设备和大数据分析。

Amazon S3 是一种数据元存储服务，可将数据以对象形式存储在存储桶中。*对象*指的是一个文件和描述该文件的任何元数据。*存储桶*是对象的容器。

* Buckets：A bucket is a container for objects stored in Amazon S3.
* Objects：Objects are the fundamental entities stored in Amazon S3.
* Keys：An *object key* (or *key name*) is the unique identifier for an object within a bucket.

使用存储桶Bucket和对象Object来开始使用 Amazon S3。*存储桶*是对象的容器。*对象*指的是一个文件和描述该文件的任何元数据。

**步骤：**

1. 创建 bucket
2. 上传 object
3. 下载 object
4. 复制 object
5. 清空 bucket & 删除 bucket



```json
# Create S3 bucket
{
    "Resources": {
        "S3BX1Q8": {
            "Type": "AWS::S3::Bucket",
            "Properties": {},
            "Metadata": {
                "AWS::CloudFormation::Designer": {
                    "id": "707f6bb8-521e-49cc-baa4-c93d800ba28c"
                }
            }
        }
    }
}
```



**Upload Objects Method:**

![image-20221227152839889](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221227152839889.png)



以下示例将创建两个对象。第一个对象将一个文本字符串作为数据，第二对象是一个文件。该示例通过在对 `AmazonS3Client.putObject()` 的调用中直接指定存储桶名称、对象键和文本数据来创建第一个对象。该示例通过使用指定存储桶、对象键和文件路径的 `PutObjectRequest` 来创建第二个对象。`PutObjectRequest` 还指定 `ContentType` 标头和标题元数据。

```java
import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.ObjectMetadata;
import com.amazonaws.services.s3.model.PutObjectRequest;

import java.io.File;
import java.io.IOException;

public class UploadObject {

    public static void main(String[] args) throws IOException {
        Regions clientRegion = Regions.DEFAULT_REGION;
        String bucketName = "*** Bucket name ***";
        String stringObjKeyName = "*** String object key name ***";
        String fileObjKeyName = "*** File object key name ***";
        String fileName = "*** Path to file to upload ***";

        try {
            //This code expects that you have AWS credentials set up per:
            // https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html
            AmazonS3 s3Client = AmazonS3ClientBuilder.standard()
                    .withRegion(clientRegion)
                    .build();

            // Upload a text string as a new object.
            s3Client.putObject(bucketName, stringObjKeyName, "Uploaded String Object");

            // Upload a file as a new object with ContentType and title specified.
            PutObjectRequest request = new PutObjectRequest(bucketName, fileObjKeyName, new File(fileName));
            ObjectMetadata metadata = new ObjectMetadata();
            metadata.setContentType("plain/text");
            metadata.addUserMetadata("title", "someTitle");
            request.setMetadata(metadata);
            s3Client.putObject(request);
        } catch (AmazonServiceException e) {
            // The call was transmitted successfully, but Amazon S3 couldn't process 
            // it, so it returned an error response.
            e.printStackTrace();
        } catch (SdkClientException e) {
            // Amazon S3 couldn't be contacted for a response, or the client
            // couldn't parse the response from Amazon S3.
            e.printStackTrace();
        }
    }
}

```



### IAM

AWS Identity and Access Management (IAM) 是一种 Web 服务，可以帮助您安全地控制对 AWS 资源的访问。可以使用 IAM 来控制谁通过了**身份验证 Identity（准许登录）并获得授权Access（拥有权限）**来使用资源。

**Authentication & Authorization**

* 身份验证 Identity - Authentication
* 授权 Access - Authorization

创建 AWS 账户时，最初使用的是一个对账户中所有 AWS 服务和资源拥有完全访问权限的登录身份。此身份称为 AWS 账户 **root 用户**，使用您创建账户时所用的电子邮件地址和密码登录，即可获得该身份。

![image-20221227154505360](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221227154505360.png)

* Users
* User group
* Roles
* Policies
* Identity providers



**用户组-用户-权限**

![image-20221227154934695](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221227154934695.png)

![image-20221227155152166](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221227155152166.png)

```json
# Create IAM user
{
    "Resources": {
        "IAMUZTWJ": {
            "Type": "AWS::IAM::User",
            "Properties": {},
            "Metadata": {
                "AWS::CloudFormation::Designer": {
                    "id": "1a82c965-c4b9-46fe-a068-5b69d6c93be6"
                }
            }
        }
    }
}
```

```json
{
   "Type" : "AWS::IAM::User",
   "Properties" : {
      "Path" : "/",
      "LoginProfile" : {
         "Password" : "myP@ssW0rd"
      },
      "Policies" : [ {
         "PolicyName" : "giveaccesstoqueueonly",
         "PolicyDocument" : {
            "Version": "2012-10-17",
            "Statement" : [ {
               "Effect" : "Allow",
               "Action" : [ "sqs:*" ],
               "Resource" : [ {
                  "Fn::GetAtt" : [ "myqueue", "Arn" ]
               } ]
            }, {
               "Effect" : "Deny",
               "Action" : [ "sqs:*" ],
               "NotResource" : [ {
                  "Fn::GetAtt" : [ "myqueue", "Arn" ]
               } ]
            }
         ] }
      }, {
         "PolicyName" : "giveaccesstotopiconly",
         "PolicyDocument" : {
            "Version": "2012-10-17",
            "Statement" : [ {
               "Effect" : "Allow",
               "Action" : [ "sns:*" ],
               "Resource" : [ { "Ref" : "mytopic" } ]
            }, {
               "Effect" : "Deny",
               "Action" : [ "sns:*" ],
               "NotResource" : [ { "Ref" : "mytopic" } ]
            } ]
         }
      } ]
   }
} 
```



### DynamoDB

Amazon DynamoDB 是一种全托管 **NoSQL 数据库服务**，提供快速而可预测的性能，能够实现无缝扩展。DynamoDB 可以免除操作和扩展分布式数据库的管理工作负担，因而无需担心硬件预置、设置和配置、复制、软件修补或集群扩展等问题。

使用 DynamoDB 创建一个数据库表来存储和检索任意量级的数据，并支持任何级别的请求流量。

DynamoDB 提供了**按需备份功能**。它允许您创建表的完整备份以进行长期保留和存档。启用**时间点恢复**有助于保护 表免遭意外写入或删除操作。



**The basic DynamoDB components:**

* Tables - 表
* Items - 行
* Attributes - 属性



**步骤：**

1. 创建表
2. 写入数据
3. 读取数据
4. 更新数据
5. 查询数据

```json
# Create DynamoDB table
{
    "Resources": {
        "DDBT3RZOY": {
            "Type": "AWS::DynamoDB::Table",
            "Properties": {},
            "Metadata": {
                "AWS::CloudFormation::Designer": {
                    "id": "044ab82a-0ddc-4f56-b2ee-e2477ee52601"
                }
            }
        }
    }
}
```

```json
# Create DynamoDB table
{
    "Resources": {
        "DDBTable": {
            "Type": "AWS::DynamoDB::Table",
            "Properties": {
                "AttributeDefinitions": [
                    {
                        "AttributeName": "ArtistId",
                        "AttributeType": "S"
                    },
                    {
                        "AttributeName": "Concert",
                        "AttributeType": "S"
                    },
                    {
                        "AttributeName": "TicketSales",
                        "AttributeType": "S"
                    }
                ],
                "KeySchema": [
                    {
                        "AttributeName": "ArtistId",
                        "KeyType": "HASH"
                    },
                    {
                        "AttributeName": "Concert",
                        "KeyType": "RANGE"
                    }
                ]
            }
        }
    }
}
```





![image-20221227175240444](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221227175240444.png)



**DynamoDB & AWS SDK**

https://docs.aws.amazon.com/zh_cn/amazondynamodb/latest/developerguide/GettingStarted.html

![image-20221228093343649](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221228093343649.png)

代码示例：https://docs.aws.amazon.com/zh_cn/amazondynamodb/latest/developerguide/service_code_examples.html

```java
# AWS DynamoDB SDK for JAVA
/**
 * This file is licensed under the Apache License, Version 2.0 (the "License").
 * You may not use this file except in compliance with the License. A copy of
 * the License is located at
 *
 * http://aws.amazon.com/apache2.0/
 *
 * This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
 * CONDITIONS OF ANY KIND, either express or implied. See the License for the
 * specific language governing permissions and limitations under the License.
*/


package com.amazonaws.codesamples.document;

import java.util.ArrayList;
import java.util.Iterator;

import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
import com.amazonaws.services.dynamodbv2.document.DynamoDB;
import com.amazonaws.services.dynamodbv2.document.Table;
import com.amazonaws.services.dynamodbv2.document.TableCollection;
import com.amazonaws.services.dynamodbv2.model.AttributeDefinition;
import com.amazonaws.services.dynamodbv2.model.CreateTableRequest;
import com.amazonaws.services.dynamodbv2.model.KeySchemaElement;
import com.amazonaws.services.dynamodbv2.model.KeyType;
import com.amazonaws.services.dynamodbv2.model.ListTablesResult;
import com.amazonaws.services.dynamodbv2.model.ProvisionedThroughput;
import com.amazonaws.services.dynamodbv2.model.TableDescription;

public class DocumentAPITableExample {

    static AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard().build();
    static DynamoDB dynamoDB = new DynamoDB(client);

    static String tableName = "ExampleTable";

    public static void main(String[] args) throws Exception {

        createExampleTable();
        listMyTables();
        getTableInformation();
        updateExampleTable();

        deleteExampleTable();
    }

    static void createExampleTable() {

        try {

            List<AttributeDefinition> attributeDefinitions = new ArrayList<AttributeDefinition>();
            attributeDefinitions.add(new AttributeDefinition().withAttributeName("Id").withAttributeType("N"));

            List<KeySchemaElement> keySchema = new ArrayList<KeySchemaElement>();
            keySchema.add(new KeySchemaElement().withAttributeName("Id").withKeyType(KeyType.HASH)); // Partition
                                                                                                     // key

            CreateTableRequest request = new CreateTableRequest().withTableName(tableName).withKeySchema(keySchema)
                .withAttributeDefinitions(attributeDefinitions).withProvisionedThroughput(
                    new ProvisionedThroughput().withReadCapacityUnits(5L).withWriteCapacityUnits(6L));

            System.out.println("Issuing CreateTable request for " + tableName);
            Table table = dynamoDB.createTable(request);

            System.out.println("Waiting for " + tableName + " to be created...this may take a while...");
            table.waitForActive();

            getTableInformation();

        }
        catch (Exception e) {
            System.err.println("CreateTable request failed for " + tableName);
            System.err.println(e.getMessage());
        }

    }

    static void listMyTables() {

        TableCollection<ListTablesResult> tables = dynamoDB.listTables();
        Iterator<Table> iterator = tables.iterator();

        System.out.println("Listing table names");

        while (iterator.hasNext()) {
            Table table = iterator.next();
            System.out.println(table.getTableName());
        }
    }

    static void getTableInformation() {

        System.out.println("Describing " + tableName);

        TableDescription tableDescription = dynamoDB.getTable(tableName).describe();
        System.out.format(
            "Name: %s:\n" + "Status: %s \n" + "Provisioned Throughput (read capacity units/sec): %d \n"
                + "Provisioned Throughput (write capacity units/sec): %d \n",
            tableDescription.getTableName(), tableDescription.getTableStatus(),
            tableDescription.getProvisionedThroughput().getReadCapacityUnits(),
            tableDescription.getProvisionedThroughput().getWriteCapacityUnits());
    }

    static void updateExampleTable() {

        Table table = dynamoDB.getTable(tableName);
        System.out.println("Modifying provisioned throughput for " + tableName);

        try {
            table.updateTable(new ProvisionedThroughput().withReadCapacityUnits(6L).withWriteCapacityUnits(7L));

            table.waitForActive();
        }
        catch (Exception e) {
            System.err.println("UpdateTable request failed for " + tableName);
            System.err.println(e.getMessage());
        }
    }

    static void deleteExampleTable() {

        Table table = dynamoDB.getTable(tableName);
        try {
            System.out.println("Issuing DeleteTable request for " + tableName);
            table.delete();

            System.out.println("Waiting for " + tableName + " to be deleted...this may take a while...");

            table.waitForDelete();
        }
        catch (Exception e) {
            System.err.println("DeleteTable request failed for " + tableName);
            System.err.println(e.getMessage());
        }
    }

}


```



### CloudWatch

Amazon CloudWatch 可实时监控 AWS 资源以及在 AWS 上运行的应用程序。您可以使用 CloudWatch 收集和跟踪指标，这些指标是您可衡量的相关资源和应用程序的变量。**Monitors AWS resources and the applications in real time**

![image-20221228113150510](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221228113150510.png)









### CloudFormation ![img](https://lh4.googleusercontent.com/okX8-3YQnD2atdMLE5iNINZxIcKi6YSkO2-H1ik0YkRwrEyHykIPuOQtmrE-F7xosKLa08uIrPc6ksG2YeppPzjdN1cxFy5XUFuqZppfUU2MM0qg-jUN3w0IxqHAJaJG6ClQ0M3MNLS3U8hv-r8T1nFEu6w2wD526qNM79lJwrW8yW0UXZfFaSs5wAg9erY=s2048)







![image-20221222110001134](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221222110001134.png)

![image-20221228093831822](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221228093831822.png)





* CloudFormation：As Code，template
* SNS：消息推送，发送者
* SQS：消息队列，订阅 SNS 消息
* Lambda：订阅 SNS 消息
* S3：对象存储
* IAM：认证和鉴权 Identity & Access
* DynamoDB：NoSQL
* CloudWatch：监控资源&应用程序 monitors AWS resources and the applications in real time





