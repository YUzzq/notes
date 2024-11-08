## 项目本地启动

### marin代码拉取

**gitlab账号解封**

- GitLab 账号自助解封流程 https://info.bilibili.co/x/feM_Hg

**呼叫外援**

- 找mentor给权限&拉进仓库

**代码拉取**

- git地址：https://git.bilibili.co/ogv/marin

**依赖安装**

- 参考git仓库的readme文档
  *tip:子应用在packges文件夹下*

### 权限申请

**权限平台**

- https://uat-manager.bilibili.co/permit/roles?auth_status=all&page_number=1

**申请权限**

- 相关权限

  ![img](https://p.ipic.vip/txjsuk.png)

### switchHost映射

● 在uat环境配置最后面加上映射关系：`127.0.0.1 local-bangumi-mng.bilibili`

### 启动

1. `pnpm dev`启动子应用
2. 浏览器访问`http://local-bangumi-mng.bilibili.co:8081/prop/list`
3. 大功告成

### 发布

**uat & pre**

1. 拉取基准分支，本地进行合并后push
2. 进入[dejavu平台](https://dejavu.bilibili.co/application/builds/320?current=1&pageSize=10)
   1. 触发流水线：![image-20241015105513067](/Users/yuzzq/Library/Application Support/typora-user-images/image-20241015105513067.png)



2. 



