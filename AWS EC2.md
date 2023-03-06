# EC2 简介

AWS EC2是Amazon Web Services（AWS）的一项核心服务，它是一种弹性计算云服务，允许用户轻松地创建和管理虚拟机实例。

AWS EC2提供了可伸缩的计算容量，用户可以根据需要快速启动和停止实例，以满足不同的计算需求。它还提供了多种实例类型，可以满足不同的计算和内存需求，并且支持多种操作系统和软件配置。

以下是使用AWS EC2的简单步骤：

1. 创建EC2实例

在AWS控制台中，选择EC2服务，然后单击“实例”选项卡，单击“启动实例”按钮，根据需要选择实例类型、操作系统和其他配置选项，并按照向导完成实例创建。

2. 连接到EC2实例

在实例创建后，可以使用SSH协议或其他远程访问工具连接到实例。在控制台中，选择“实例”，然后单击实例名称，在“描述”选项卡中找到实例的公共IP地址或DNS名称，使用这些信息连接到实例。

3. 配置安全组

安全组是一种虚拟防火墙，用于控制进出EC2实例的网络流量。在控制台中，选择“安全组”，然后创建一个新的安全组或编辑现有的安全组，根据需要打开所需的端口。

4. 部署应用程序

可以在EC2实例上部署应用程序，如网站、API服务、数据库等。部署过程取决于应用程序类型和所需的软件配置。可以使用AWS提供的各种服务和工具，如Elastic Beanstalk、OpsWorks、CodeDeploy等来简化和自动化部署过程。

5. 管理实例

可以使用AWS控制台或AWS CLI来管理EC2实例，如启动、停止、重启、修改实例配置、监控实例性能等。可以通过创建自动化脚本或使用第三方工具来自动化实例管理过程。

总结

AWS EC2是一种弹性计算云服务，提供了可伸缩的计算容量和多种实例类型。通过创建EC2实例、连接到实例、配置安全组、部署应用程序和管理实例等步骤，用户可以在AWS云中轻松地创建、部署和管理应用程序，提高计算效率和灵活性。



## 使用 AWS Cloudformation 创建 EC2 实例

使用AWS CloudFormation创建EC2实例的步骤如下：

1. 创建模板

首先，需要创建一个CloudFormation模板，该模板描述了要创建的EC2实例和相关资源。可以使用JSON或YAML格式创建模板。

例如，以下是一个简单的CloudFormation模板，用于创建一个EC2实例：

```yaml
Resources:
  EC2Instance:
    Type: 'AWS::EC2::Instance'
    Properties:
      InstanceType: t2.micro
      ImageId: ami-0c94855ba95c71c99
      KeyName: my-keypair
```

该模板创建一个t2.micro实例，使用Amazon Linux 2 AMI，以及一个名为“my-keypair”的SSH密钥对。

2. 创建堆栈

在AWS控制台中，选择CloudFormation服务，然后单击“创建堆栈”按钮，选择模板文件，然后按照向导完成堆栈创建。在创建堆栈过程中，需要指定堆栈名称、EC2实例名称、SSH密钥对和其他配置选项。

3. 查看堆栈状态

在堆栈创建完成后，可以在CloudFormation控制台中查看堆栈状态。如果堆栈状态为“CREATE_COMPLETE”，则表示EC2实例已经创建成功。

4. 连接到EC2实例

在实例创建完成后，可以使用SSH协议或其他远程访问工具连接到实例。在控制台中，选择“实例”，然后单击实例名称，在“描述”选项卡中找到实例的公共IP地址或DNS名称，使用这些信息连接到实例。

总结

AWS CloudFormation是一种服务，用于创建和管理AWS资源的模板。通过创建CloudFormation模板，可以轻松地创建EC2实例和其他AWS资源。可以在AWS控制台中创建堆栈，使用CloudFormation模板来自动化EC2实例的创建过程。



## 使用 SSH 连接到 EC2 实例

1. 获取EC2实例的公共IP地址

在EC2实例创建完成后，可以在AWS控制台中找到实例的公共IP地址。在EC2实例列表中，找到目标实例并单击实例ID，在“描述”选项卡中可以找到公共IP地址。

2. 配置安全组规则

在EC2实例中，需要设置安全组规则，以便允许SSH连接。默认情况下，安全组可能不会允许SSH连接。

在AWS控制台中，选择“安全组”，然后选择实例的安全组。在入站规则中添加SSH规则，允许SSH协议和端口22。出站规则默认为全部允许。

3. 配置本地SSH客户端

在本地计算机上，需要安装SSH客户端。对于Windows操作系统，可以使用PuTTY或Git Bash等SSH客户端。对于Mac或Linux操作系统，可以使用终端应用程序。

4. 使用SSH客户端连接到EC2实例

使用SSH客户端连接到EC2实例需要以下命令：

```shell
ssh -i "key.pem" ec2-user@public-ip-address
```

其中，key.pem是EC2实例的私钥文件，public-ip-address是EC2实例的公共IP地址。

例如，如果使用PuTTY SSH客户端，可以在“Session”选项卡中输入EC2实例的公共IP地址和端口22，并在“Auth”选项卡中指定EC2实例的私钥文件。

总结

通过SSH连接到EC2实例，可以使用SSH协议在本地计算机上与EC2实例进行交互。在连接之前，需要获取EC2实例的公共IP地址，并设置安全组规则，允许SSH连接。同时，需要在本地计算机上安装SSH客户端，并使用SSH协议连接到EC2实例，使用EC2实例的私钥文件进行身份验证。



## 配置 EC2 安全组

EC2安全组是一种虚拟防火墙，用于控制进出EC2实例的网络流量。在创建EC2实例时，需要指定安全组，以便控制EC2实例的网络访问。如果需要更改安全组规则，可以按照以下步骤进行操作：

1. 登录到AWS控制台，选择EC2实例
2. 在“实例”选项卡中选择需要更改安全组规则的EC2实例，单击右键，在弹出菜单中选择“安全组”->“查看/编辑入站规则”或“查看/编辑出站规则”。
3. 在安全组规则编辑界面中，可以添加、删除或修改规则。根据需要添加端口号、协议、IP地址范围等规则。例如，如果需要允许SSH访问EC2实例，可以添加入站规则，允许SSH协议和端口号22。
4. 单击“保存规则”按钮，保存安全组规则。保存后，新的规则将立即生效。

需要注意的是，更改安全组规则可能会影响EC2实例的网络访问。如果更改了入站规则，可能会导致无法从外部访问EC2实例的某些服务。如果更改了出站规则，可能会导致EC2实例无法访问外部网络。因此，需要仔细考虑安全组规则的更改，以避免影响EC2实例的正常运行。

总结：

EC2安全组是控制EC2实例网络访问的重要工具，可以根据需要添加、删除或修改规则，控制网络流量。在更改安全组规则之前，需要仔细考虑规则的影响，避免影响EC2实例的正常运行。



### 使用 Cloudformation 配置 EC2 安全组

可以使用CloudFormation来配置EC2实例的安全组。CloudFormation是AWS提供的一种基于模板的服务，可以实现基础设施即代码，将整个基础设施以模板的形式进行管理和部署。以下是使用CloudFormation来配置EC2安全组的步骤：

1. 创建CloudFormation模板：创建一个JSON或YAML格式的CloudFormation模板，并定义EC2实例及其安全组。在模板中，可以指定实例类型、AMI ID、实例名称、子网ID、安全组等信息。
2. 部署CloudFormation堆栈：将CloudFormation模板部署为堆栈，创建EC2实例及其安全组。在部署过程中，需要指定堆栈名称、AWS区域、实例参数等信息。
3. 验证EC2实例及其安全组：在EC2控制台中验证EC2实例及其安全组是否已正确配置。可以检查EC2实例的安全组，查看入站和出站规则是否符合预期。

以下是一个简单的CloudFormation模板示例，用于创建具有SSH访问权限的EC2实例：

```yaml
---
AWSTemplateFormatVersion: '2010-09-09'
Description: 'EC2 Instance with SSH access'
Resources:
  EC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t2.micro
      ImageId: ami-0c94855ba95c71c99
      SecurityGroupIds:
        - !Ref EC2SecurityGroup
      KeyName: mykeypair
  EC2SecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Enable SSH access
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0
```

在以上模板中，EC2Instance和EC2SecurityGroup两个资源均被定义。其中，EC2SecurityGroup定义了安全组，允许SSH访问端口22。EC2Instance定义了EC2实例，引用了EC2SecurityGroup作为安全组。

总结：

使用CloudFormation可以实现基础设施即代码，快速创建和配置EC2实例及其安全组。可以通过定义模板中的资源来指定实例类型、AMI ID、安全组等信息，同时也可以通过模板来控制安全组规则。使用CloudFormation可以实现自动化的EC2实例部署和管理，提高效率和可维护性。