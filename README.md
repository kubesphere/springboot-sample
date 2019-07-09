# 示例一：使用 DevOps 工程快速发布 Spring Boot 项目

本示例使用 KubeSphere 的 DevOps 工程将 [Spring Boot 示例项目](https://github.com/kubesphere/springboot-sample) 以 CI/CD 流水线的形式部署至 Kubernetes 环境中。
## 演示视频
[点此播放](https://yunify.anybox.qingcloud.com/s/WqYExnCCBlNXPd5SivWjql8mkkp2bm1f)
## 前提条件
已有 [GitHub](https://github.com/) 和 [DockerHub](http://www.dockerhub.com/) 账号。
## 第一步：创建企业空间、项目和 DevOps 工程
**1.1.** 使用 admin 登录 KubeSphere，创建一个企业空间示例 **demo-workspace**。
>提示：为方便演示本示例暂用 admin 账户登录操作，实际场景需由不同角色权限的用户操作

![图片](https://uploader.shimo.im/f/ShZhxgPEQT0xrowU.png!thumbnail)
**1.2.** 在该企业空间下，依次创建一个**项目**和一个 **DevOps 工程，**信息如下 (高级设置默认即可)。
| 项目 (名称)   |  kubesphere-sample-dev   | 作为开发环境   | 
|:----|:----|:----|
| DevOps 工程 (名称)   |  devops-demo   | 用于创建流水线   | 

![图片](https://uploader.shimo.im/f/P5z2kaYHkE0fU0TU.png!thumbnail)
**新建的项目**
![图片](https://uploader.shimo.im/f/aApJhSjBJKgacxx3.png!thumbnail)
**新建的 DevOps 工程**
![图片](https://uploader.shimo.im/f/mh49PtJZXkgTCI8J.png!thumbnail)

## 第二步：创建凭证
进入上述 DevOps 工程，在左侧菜单栏的**凭证**下点击 「创建凭证」，如下创建 3 个凭证：
| **凭证 ID **   | ** 类型**   | **用户名密码/Content**   | 
|:----:|:----|:----|
| dockerhub-id | 账户凭证   | 您 DockerHub 的用户名密码   | 
| github-id   | 账户凭证   | 您 GitHub 的用户名密码   | 
| demo-kubeconfig   | kubeconfig   | 将自动获取集群的kubeconfig   | 

![图片](https://uploader.shimo.im/f/5zCUQoIH43sKqEAt.png!thumbnail)

## 第三步：Fork 代码仓库并修改 Jenkinsfile
**3.1.** 登录 GitHub，将本示例用到的 GitHub 仓库 [SpringBoot-Sample ](https://github.com/kubesphere/springboot-sample.git)Fork 至您个人的 GitHub。
![图片](https://uploader.shimo.im/f/MROUpHPonYAugGIG.png!thumbnail)
**3.2.** 在示例仓库中修改 Jenkinsfile，将 **DOCKERHUB_NAMESPACE **和 **GITHUB_ACCOUNT** 的值替换为您实际的账号名或 Organization 名称。
![图片](https://uploader.shimo.im/f/o0d19aVTNgcqhwlW.png!thumbnail)

## 第四步：创建流水线
**4.1.** 在当前 DevOps 工程下创建一个流水线，点击代码仓库，以添加 Github 仓库为例，在弹窗点击 [获取 Token](https://github.com/settings/tokens/new?scopes=repo,read:user,user:email,write:repo_hook)。
![图片](https://uploader.shimo.im/f/WYDOFIyx2o8TBSGc.png!thumbnail)
**4.2. **在 GitHub 页面简述该 token，点击 **Generate token**，GitHub 将生成一串字母和数字组成的 Token 用于访问当前账户下的代码仓库。复制 Token 然后返回 KubeSphere 填入 Token，点击**确认。**

**4.3.** 然后在右侧选择在上一步 Fork 的示例代码仓库 springboot-sample。
![图片](https://uploader.shimo.im/f/LYv9vqJQXhYJnBu3.png!thumbnail)
**4.4.** 下一步高级设置中，在行为策略中将 “**从 Fork 仓库中发现 PR**” 删除，然后将扫描时间间隔设置为 **5 min。**完成设置后，点击创建。
>说明：定期扫描是设定一个周期让流水线周期性地扫描远程仓库，根据设置的“行为策略”查看仓库有没有代码更新或新的 PR，可根据团队开发习惯设置。

![图片](https://uploader.shimo.im/f/lwvhCWwHqgoxhHMF.png!thumbnail)

**4.5.** 刷新一下，在活动列表中将自动触发一条流水线，点击 「运行」，在弹窗中填入 TAG_NAME 为 v0.0.1，生成的 Tag 将被 push 到 GitHub 和 DockerHub。点击 「确定」，将生成一条新的流水线活动并开始运行。
![图片](https://uploader.shimo.im/f/AbhPb07HxeYf3i5W.png!thumbnail)
![图片](https://uploader.shimo.im/f/8tbeAQQw5Ggpjdso.png!thumbnail)

**4.6.** 几分钟后，点击进入该流水线活动，查看运行状态，可以看到流水线自动读取到了示例仓库中 [Jenkinsfile](https://github.com/kubesphere/springboot-sample/blob/master/Jenkinsfile) 的所有阶段 (Stage) 和步骤 (Step)。目前已自动运行至 deploy to dev 阶段，需要其他用户来审核流水线，为演示方便这里直接使用 admin 审核，点击 「继续」。
>提示： deploy to dev 和 push with tag 都需要点击继续。

![图片](https://uploader.shimo.im/f/neFRZgDWviI4wyHl.png!thumbnail)
## 第五步：查看流水线并验证运行结果
**5.1.** 稍等2分钟流水线可运行成功，流水线构建的 tag 和镜像将分别被自动 push 到 GitHub 和 DockerHub，仓库中的 Spring Boot 示例应用也将被部署至 **kubesphere-sample-dev** 项目中。
![图片](https://uploader.shimo.im/f/b1bBUlHmZJEEo4P8.png!thumbnail)
**5.2.** 回到第一步创建的项目  **kubesphere-sample-dev** 下，进入 **工作负载 → 部署** 下可以看到 spring boot 的 Deployment 状态为 “运行中”。在 **网络与服务→服务 **下可以看到服务的端口信息。
![图片](https://uploader.shimo.im/f/R1MkLRjFFEsmYFEV.png!thumbnail)
![图片](https://uploader.shimo.im/f/e2MsB8d5ic005Bzl.png!thumbnail)
**5.3.** 在您 **GitHub** 示例仓库和 **DockerHub** 账号下，可以看到 v0.0.1 和 latest 的 tag，以及release 和 image。
![图片](https://uploader.shimo.im/f/nLKIdHfoyBIlbWb1.png!thumbnail)
![图片](https://uploader.shimo.im/f/mXZLILRtXY4LBrB0.png!thumbnail)
**5.4.** 在 KubeSphere 点击右下角的**工具箱**选择 **kubectl，**通过 curl Virtual IP:Port 或 NodeIP:NodePort ，或浏览器输入 URL 都可以访问到 Spring Boot 示例服务。

```
# curl {$Virtual IP}:{$Port} 或 curl {$节点 IP}:{$节点端口}
$ curl 10.233.58.117:8080
Hello,World!
```
![图片](https://uploader.shimo.im/f/Le4zMQfVDzQ9Z6YQ.png!thumbnail)


# 示例二：基于Spring Boot仓库快速构建镜像部署应用

KubeSphere 使用 [Source to Image (S2I) ](https://github.com/kubesphere/s2irun)帮助用户在不需要编写 Dockerfile 的情况下自动构建镜像并将应用部署至 Kubernetes 环境中。
## 演示视频
[点此播放](https://www.bilibili.com/video/av57389046/?p=2)
## 前提条件
已有 [GitHub](https://github.com/) 和 [DockerHub](http://www.dockerhub.com/) 账号，并已将 [Spring Boot 示例仓库](https://github.com/kubesphere/springboot-sample) Fork 至您个人 GitHub 账号。
## 第一步：创建企业空间和项目
**1.1.** 使用 admin 登录 KubeSphere，创建一个企业空间示例 **demo-workspace**。
>提示：为方便演示本示例暂用 admin 账户登录操作，实际场景需由不同角色权限的用户操作

![图片](https://uploader.shimo.im/f/ShZhxgPEQT0xrowU.png!thumbnail)
**1.2.** 在该企业空间下，依次创建一个示例**项目** demo-project (高级设置默认即可)。

![图片](https://uploader.shimo.im/f/P5z2kaYHkE0fU0TU.png!thumbnail)
**新建的项目**
![图片](https://uploader.shimo.im/f/W0j3zOeJfr0LJ9Qh.png!thumbnail)

## 第二步：创建密钥和部署
**2.1.** 进入 demo-project 示例项目中，在 **配置中心→密钥 **下点击 「创建」，分别创建 GitHub 和 DockerHub 的密钥。
| **密钥名称**   | **类型**   | **仓库地址**   | **用户名/密码**   | 
|:----:|:----:|:----:|:----:|
| dockerhub-id | 镜像仓库密钥 | docker.io | 您 DockerHub 用户名和密码 | 
| github-id | 账号密码密钥 | /   | 您 GitHub 用户名和密码   | 


![图片](https://uploader.shimo.im/f/MfG4sjHEDwAaU8wl.png!thumbnail)
**新建的密钥**
![图片](https://uploader.shimo.im/f/TWsHUwFWWi0FqByK.png!thumbnail)
**2.2.** 左侧导航栏选择 **工作负载→部署**，点击 「**创建部署**」，自定义名称 (如 springboot-s2i)，然后点击「下一步」。
![图片](https://uploader.shimo.im/f/bMgzwQCaZE4MimcT.png!thumbnail)

**2.3.** 点击 「添加容器」，选择 **通过代码构建新的容器镜像**，镜像信息设置参考如下提示：
* 代码地址：https://github.com/{$YOUR_GITHUB_ACCOUNT}/springboot-sample
* 镜像模板：选择 **kubesphere/java-8-centos7:advanced-2.0.0 **
* 镜像名称：{$YOUR_DOCKERHUB_ACCOUNT}/springboot-s2i (tag 默认 latest 即可)
* 密钥：选择上一步创建的密钥 github-id
* 目标镜像仓库：选择上一步创建的密钥 dockerhub-id

![图片](https://uploader.shimo.im/f/5pVH1rNxJPMnb2XN.png!thumbnail)
**2.4.** 下拉至服务设置，自定义端口名，端口号为 **8080**。然后点击保存，
![图片](https://uploader.shimo.im/f/aofawqhIJkAoLa6u.png!thumbnail)
**2.5.** 部署状态显示正在构建镜像，同时将创建一个 S2i 任务 (Job) 来完成镜像的构建和推送。
![图片](https://uploader.shimo.im/f/u3HF0aRbW74AGDDK.png!thumbnail)
## 第三步：创建服务
**3.1.** 左侧导航栏选择 网络与服务 → 服务，点击 「创建服务」。自定义服务名 (如 s2i-service)，下一步选择第一项 **通过集群内部IP来访问服务 Virtual IP，**然后点击下一步。
![图片](https://uploader.shimo.im/f/Alw4YmbO6GMIiUUf.png!thumbnail)
**3.2.** 点击 「指定工作负载」，选择第二步创建的部署 springboot-s2i，端口信息设置如下。
![图片](https://uploader.shimo.im/f/9QghIKdQdn0C1iDc.png!thumbnail)

**3.3.** 点击 「下一步」，外网访问选择 **NodePort**，将通过节点端口暴露服务，点击 「创建」，即可看到服务创建成功，端口号信息如图所示。
![图片](https://uploader.shimo.im/f/QSjj04ZgbK4nfWZC.png!thumbnail)

## 第四步：验证服务和镜像推送
当镜像构建完成后，部署显示运行中，此时可通过 KubeSphere 点击右下角的**工具箱**选择 **kubectl，**通过 curl Virtual IP:Port 或 NodeIP:NodePort ，或浏览器输入 URL 都可以访问到 Spring Boot 示例服务。
```
# curl {$Virtual IP}:{$Port} 或 curl {$节点 IP}:{$节点端口}
$ curl 10.233.39.207:8080
Hello,World!
```
![图片](https://uploader.shimo.im/f/UypPHouA5DcnnhhP.png!thumbnail)
**查看 DockerHub**

![图片](https://uploader.shimo.im/f/p1owgr5HeBA1aNCW.png!thumbnail)


