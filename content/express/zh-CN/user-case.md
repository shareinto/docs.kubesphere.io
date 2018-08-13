---
title: "快速入门 - 部署 WordPress 博客网站"
---

WordPress 是使用 PHP 语言开发的博客平台，用户可以在支持 PHP 和 MySQL 数据库的服务器上架设属于自己的网站, 也可以把 WordPress 当作一个内容管理系统（CMS）来使用。在本指南中，将引导用户通过 KubeSphere 控制台部署一个后端为 MySQL 数据库的 WordPress 博客网站，帮助您快速入门 KubeSphere。

## 登录 KubeSphere

1、在开始实践之前，请以普通用户（Regular）的身份登录 KubeSphere，用户身份需要通过管理员创建，关于如何管理角色请参考 [角色管理说明](/express/zh-CN/manage-roles/)，关于成员管理请参考 [用户管理说明](/express/zh-CN/manage-users/)。

## 创建项目

2、登录后，通过首页或项目管理创建项目。

> 说明: 关于项目管理的详细介绍，请参考 [项目管理说明](/express/zh-CN/manage-projects/)。

![](/uc_homepage.png)


![](/uc_createproj.png)


## 创建存储卷

3、在菜单栏的资源中选择存储卷，点击创建存储卷，按如下步骤分别为 WordPress 和 MySQL 数据库创建存储卷，可命名为 wordpress-volume 和 mysql-volume。

>说明：关于存储卷管理和使用的详细介绍, 请参考 [存储卷使用说明](/express/zh-CN/manage-storages/)。

![](/uc_createpv.png)

4、创建 WordPress 存储卷 `wordpress-volume` 需填写基本信息、配置存储设置和标签设置，请参考以下步骤：

- 第一步，填写存储卷基本信息，完成后点下一步：

![](/uc_createpv1.png)

- 第二步，选择存储类型 (请参考 [存储类型管理说明](/express/zh-CN/manage-storageclasses/) 通过管理员创建存储类型)，设置存储大小和读写模式，完成后点下一步：

![](/uc_createpv2.png)

- 第三步，填写标签并保存，点击创建。在存储卷列表页，即可看到 WordPress 所需的存储卷创建成功 (刚完成创建时存储卷状态为 Pending 是正常的，等待数秒后状态将自动更新为 Bound）：

![](/uc_createpv3.png)


5、同上，创建 MySQL 所需存储卷 `mysql-volume`，参考上述步骤完成基本信息、存储设置和标签设置。至此，WordPress 和 MySQL 所需的存储卷都创建成功：

![](/uc_createpv7.png)

## 创建部署

6、在菜单栏的应用负载中选择部署，点击创建部署，安装如下步骤分别为 WordPress 和 MySQL 数据库创建部署资源，可命名为 wordpress 和 wordpress-mysql：

> 说明：关于管理部署资源的详细介绍，请参考 [部署管理说明](/express/zh-CN/manage-deployments/)。

![](/uc_createdeploy.png)


7、请参考以下步骤创建部署 wordpress-mysql：

- 第一步，填写创建部署的基本信息，完成后点下一步：

![](/uc_createdeploy9.png)

- 第二步，填写容器组设置，名称可由用户自定义，镜像填写 `mysql:5.6`（应指定镜像版本号)，高级选项中可以对 CPU 和内存的资源使用进行限定, 此处暂不作限定。其中 **requests** 是集群保证分配给容器的资源， **limits** 是容器可以使用的资源的上限。用户可以通过**命令**和**参数** 选项自定义容器的启动命令和启动参数:

![](/uc_createdeploy10.png)

> 注: 如果 docker 镜像不是来自默认的 Docker Hub, 请参考 [镜像仓库管理说明](/express/zh-CN/manage-imageregistries/)。

- 在容器组设置中配置 MySQL 的访问端口和 MySQL 的环境变量 `MYSQL_ROOT_PASSWORD` 和 root 用户的密码 , **端口**用于指定容器需要暴露的端口，端口协议可以选择 TCP 和 UDP, 用户还可以指定端口与主机端口进行绑定，**环境变量**可以指定容器内部使用的环境变量。完成后点下一步：

![](/uc_createdeploy11.png)

- 第三步，存储设置中，点击添加存储卷并选择已有存储卷，然后选择之前创建好的 mysql-volume 存储卷：

> 注：如果安装 KubeSphere 使用的是 all-in-one 部署在单个主机，使用的存储类型是 Local Volume， 请参考存储卷使用说明的 [附录1: Local Volume 使用方法](/express/zh-CN/manage-storages/#附录1)。 如果集群配置的存储服务端是 Ceph RBD，则需要通过 Kubectl 创建 Secret， 若遇到 Ceph RBD 存储卷挂载至工作负载时因缺少密钥无法挂载, 请参考存储卷使用说明的 [附录2: Ceph RBD 存储卷缺少密钥无法挂载解决方案](/express/zh-CN/manage-storages/#附录2)。

![](/uc_createdeploy12.png)

- 填写存储卷在容器中的挂载路径，填写后点击挂载，选择下一步：

![](/uc_createdeploy13.png)

- 第四步，设置标签并保存，选择下一步：

![](/uc_createdeploy14.png)

- 第五步，节点选择器可将容器组调度到期望运行的节点上，节点选择器可根据实际情况配置。此处不作配置，选择创建，则 wordpress-mysql 部署创建成功（刚完成创建时部署状态为 “更新中” 是正常的，系统为其拉取镜像和调度资源需要时间，等待数秒后状态将自动更新为运行中）:

![](/uc_createdeploy7.png)


> 注: MySQL 数据库还可通过创建有状态副本集的方式来创建。

8、同上，请参考以下步骤创建部署 wordpress :

- 第一步，填写创建部署的基本信息，本示例创建的 wordpress 部署名称 `wordpress`，选择之前创建的项目 `lab`，副本数为 `1`，描述信息可自定义，完成后点下一步。

- 第二步，填写容器组设置，名称可自定义，镜像填写 `wordpress:4.8-apache`，配置 WordPress 的访问端口 (80 端口)和关联 MySQL 数据库的环境变量 (`WORDPRESS_DB_HOST` 和 `WORDPRESS_DB_PASSWORD`) 并保存，CPU 和内存暂不作限定，参考如下配置，完成后点下一步。

> 注意:  环境变量中, `WORDPRESS_DB_HOST` 的值对应的是 MySQL 服务的名称, 在后续步骤创建 MySQL 服务时, 服务名应该与此处的环境变量值相同, 否则无法连接 MySQL 数据库。

![](/uc_createdeploy2.png)


- 第三步，存储设置中，点击添加存储卷并选择已有存储卷，然后选择之前创建好的 wordpress-volume 存储卷，填写存储卷在容器中的挂载路径，本示例中选择 `读写`，挂载点为 `/var/www/html`，填写后点击挂载，完成后选择下一步。


- 第四步，设置标签并保存，本示例标签的键值对设置如下，完成后选择下一步。

> app=wordpress <br>
> tier=frontend


- 第五步，节点选择器此处暂不作配置，点击创建，则 WordPress 部署创建成功。 至此，WordPress 和 MySQL 部署都创建成功：

![](/uc_createdeploy15.png)



## 创建服务

9、通过服务或应用路由的方式，可以将部署的资源暴露出去以供访问，以下步骤将分别介绍如何以服务和应用理由两种方式暴露 WordPress 供外部访问。先以创建服务为例，左侧菜单栏处选择服务与网络 → 服务，点击创建服务：

> 说明： 关于管理服务的详细介绍，请参考 [服务管理说明](/express/zh-CN/manage-services)

![](/uc_createsvc.png)

10、请参考以下步骤为 MySQL 数据库创建服务：

- 填写基本信息，选择下一步：

![](/uc_createsvc1.png)

- 参考以下参数完成参数设置。其中服务类型包括 Virtual IP、Headless (selector) 和 Headless (externalname) 三种, 此处我们选择 Virtual IP， 选择器一栏选择已创建的部署: `wordpress-mysql`, MySQL 端口填写 `3306`,  选择下一步：

> 说明: 若要实现基于客户端 IP 的会话亲和性，可以在会话亲和性下拉框选择 "ClientIP" 或在代码模式将 service.spec.sessionAffinity 的值设置为 "ClientIP" （默认值为 "None"）。

![](/uc_createsvc2.png)

- 添加标签并保存，选择下一步：

![](/uc_createsvc3.png)

- 设置外网访问中，共有 None、 NodePort、 LoadBalancer 三种访问方式，可根据情景来设置访问方式。由于 MySQL 数据库不需要暴露服务，因此选择 None，即可完成创建：

![](/uc_createsvc4.png)


11、同上，请参考以下步骤为 WordPress 创建服务：

- 第一步，填写基本信息，本示例服务名称为 `wordpress-service`，选择之前创建好的项目`lab`，描述信息可自定义，完成后选择下一步。

- 第二步，参考以下参数, 其中类型选择 Virtual IP, 选择器选择之前创建好的 wordpress 部署, 访问端口选择 TCP 协议的 80 端口, 完成参数设置，选择下一步。

![](/uc_createsvc6.png)

- 第三步，添加标签并保存，本示例标签设置如下，添加后选择下一步。

> tier=wordpress-service


- 第四步，设置外网访问时，共有 None、 NodePort、 LoadBalancer 三种访问方式，可根据情景来设置访问方式。如果用 LoadBalancer 的方式暴露服务，需要预先安装 LoadBalancer，并将公网 IP 地址的 `ID` 填入 Annotation 中，即可通过公网 IP 访问该服务。本实践选择 `Nodeport` 访问方式，集群外部可通过访问集群节点的对应端口来访问服务， 端口将由集群自动创建 (端口号将生成在服务的列表页中) 。至此，WordPress 与 MySQL 服务都已经创建成功。

>注： 如果主机的公网 IP 有防火墙， 应在防火墙下放行自动创建的端口，否则外网无法访问。

![](/uc_createsvc9.png)

- 第五步，通过主机的公网 IP 和自动创建的端口号访问 WordPress：

![](/uc_createsvc10.png)

## 创建应用路由

12、通过创建应用路由的方式也可以将 WordPress 暴露出去可供外部访问，不同之处是应用路由是通过配置的 Hostname 来访问。请参考以下步骤配置应用路由：

> 说明： 关于如何管理应用路由，详细请参考 [应用路由管理说明](/express/zh-CN/manage-routers/)。

- 第一步，配置外网访问入口，即应用路由的网关入口：

![](/uc_createingress.png)


- 网关入口提供 NodePort 和 LoadBalancer 两种访问方式，可根据情景需要来设置访问方式，本实践以 NodePort 访问方式为例配置网关入口，此方式网关可以通过工作节点对应的端口来访问，配置完成后点击应用 (端口显示在左边的节点端口处)：

![](/uc_createingress1.png)

- 第二步，创建应用路由，填入基本信息：

![](/uc_createingress2.png)

- 第三步，配置路由规则，这里以 `kubesphere.wp.com` 为例，并且 path 选择之前的创建成功的服务 wordpress-service，选择下一步：

>Hostname: 应用规则的访问域名，最终使用此域名来访问对应的服务。(如果访问入口是以 NodePort 的方式启用，需要保证 Host  能够在客户端正确解析到集群工作节点上；如果是以 LoadBalancer 方式启用，需要保证 Host 正确解析到负载均衡器的 IP 上)<br>
Paths: 应用规则的路径和对应的后端服务，端口需要填写成服务的端口。

![](/uc_createingress3.png)


- 第四步，添加注解，Annotation 是用户任意定义的“附加”信息，以便于外部工具进行查找 （可参见 [Annotation 官方文档](https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/) ）。本示例注解如下所示，完成后选择下一步：

>hostname=kubesphere.wp.com

- 第五步，添加标签，本示例标签设置如下，添加后选择创建：

>tier=wordpress-ingress

- 至此，WordPress 就以应用路由的方式通过网关入口暴露到外网以供访问，用户可以通过示例中配置的 `kubesphere.wp.com` 和端口号访问 WordPress 博客网站：

> 注: 创建应用路由之后应该把主机的公网 IP 和 配置的域名如：`139.198.17.33 kubesphere.wp.com` 填入本地的 hosts 配置文件中，即可通过浏览器访问。如果主机的公网 IP 有防火墙， 应在防火墙下放行自动创建的端口，否则外网无法访问。

![](/uc_createingress6.png)



## 副本数调节

13、在实际生产系统中，我们经常会遇到某个服务需要扩容的场景，也可能会遇到由于资源紧张或者工作负载降低而需要减少服务实例数的场景。在 kubectl 中我们可以利用命令 `kubectl scale rc` 来完成这些任务。但是，在 KubeSphere 中只需要通过资源详情页中左下角处的容器组数量加减按钮，来调整容器组数量，进而完成对 Pod 的动态扩容与缩放。

![](/uc_scalepods.png)


## 删除资源

14、在实践完成后，建议删除不需要的部署和服务资源，在资源列表中勾选需要删除的资源，例如下图中点击删除部署按钮可删除选中的部署资源， 详细可参考 [用户指南](#用户指南) 中的应用负载管理、服务与网络和资源管理部分，进一步释放资源。
![](/uc_rmresource.png)

## Debug

在创建资源后如果遇到报错或资源一直处于 Pending 状态，可通过资源详情页中的事件页面和容器日志查看报错消息，进而排查问题的原因。例如下图中，由于 wordpress 部署资源中的容器组状态显示 CrashLoopBackOff ，因此可以通过事件详情页中查看具体的错误消息，进一步针对性地解决问题。
![](/uc_debug.png)

若服务创建后无法访问或外部 IP 一直处于 Pending 状态, 可通过服务详情页中查看应用负载和容器组的运行状态, 如果运行都正常再查看事件页面检查是否有报错消息。例如下图中, 由于公网 IP 与 KubeSphere 主机集群不在一个 region 导致无法解析到该公网 IP, 因此服务无法暴露出去。
![](/uc_debug1.png)