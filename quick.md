{{indexmenu_n>2}}

# 快速上手

## 1\. 进行基本设置

![](/images/quick/uflink-基本设置.png)

（1）选择地域和可用区。当前支持北京二可用区E。

（2）选择VPC和子网。请注意不同地域之间内网不互通。

（3）设置集群名称和业务组。

## 2\. 选择软件版本

![](/images/quick/uflink-软件设置.png)

当前支持的Flink版本为1.6.4。

## 3\. 进行资源设置

![](/images/quick/uflink-资源设置.png)

（1）选择Master/Slave节点的机型和数量。Master节点固定为3台，Slave节点最少为1台。

（2）选择UFile存储空间和令牌。UFile存储空间用于上传任务(Job)运行所需的jar包，绑定的令牌必须具有上述存储空间的下载权限。

## 4\. 设置访问Dashboard的账号和密码

![](/images/quick/uflink-访问设置.png)

## 5\. 选择计费方式

![](/images/quick/uflink-费用.png)

支持按小时、按月、按年三种计费模式，其中选择“月付 - 购至月末”时，即从当前时刻购买至本月月底，下个月的账单从1日开始正常计费结算。

点击“立即购买”支付费用后，系统即按照上述设置创建Flink集群。