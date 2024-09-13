# Dify on Azure
## Overview
- 这是一个如何在 Azure 上部署 Dify 项目的简单示例。
- 部署之前也可以体验一下这个部署好的例子体验一下：http://dify-gpschina.eastus2.cloudapp.azure.com/ 登陆用户名/密码(无编辑权限)：dify2@gmail.com/dify1234
## Topology
![Topology](./images/image.png)

### 在配置 Dify 之前，请检查并设置 dev-variables.tfvars 文件中的变量。

在 dev-variables.tfvars 文件中，定义了6个变量：

- sp-subscription-id Azure订阅ID
- sp-client-id：这是服务主体的客户端 ID。它是一个唯一标识符，用于标识特定的服务主体。
- sp-client-secret：这是服务主体的客户端密钥。它类似于密码，用于验证服务主体的身份。这个值应当保密，不应公开。
- sp-tenant-id：这是租户 ID。它标识了 Azure Active Directory (AAD) 中的特定租户，通常用于多租户环境中区分不同的组织或用户组。
- name：Dify平台的名字，用于创建或定义多个服务的名字（比如Azure资源组，Azure Private DNS zone等），需要全球唯一。
- filename ：指定您的本地电脑中存储AKS凭据文件kubeconfig的路径
  
```bash
az login
az ad sp create-for-rbac --name <your-service-principal-name> --role Owner --scopes /subscriptions/<your-subscription-id>



```

### 执行上述命令后，你将获得一个 JSON 响应，其中包含 appId（即 sp-client-id）、password（即 sp-client-secret）和 tenant（即 sp-tenant-id）。

示例输出：
```bash
{
  "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "displayName": "<your-service-principal-name>",
  "name": "http://<your-service-principal-name>",
  "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```
### 将这些值填入你的 dev-variables.tfvars 文件中：（appId即sp-client-id，password即sp-client-secret，tenant即sp-tenant-id）
```bash
sp-client-id = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
sp-client-secret = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
sp-tenant-id = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```
- 注意：本repo默认在Windows下运行Terraform，如果运行Terraform使用Mac或Linux的话，需要在
DIFYONAZURE/modules/dify/main.tf文件中修改chat的路径格式如下：

#### chart = "./helm-release/dify-helm/charts/dify"

## Deploy
```bash
terraform init
terraform plan -out=dev-plan -var-file="./environments/dev-variables.tfvars"
terraform apply "dev-plan"
```
