# gitlab ci 的搭建

## 第一部分 概述
### 1.1 使用的内容介绍：
1. gitlab-ci 是使用的gitlab  加上gitlab-runner 完成整个持续集成操作。
2. 使用的gitlab-runner 的执行器是 docker 。 所以必须会docker 的基本操作。
3. 然后必须对gitlab-ci 的操作有基本的认知。

### 1.2 搭建ci 的基础服务内容有：
1. 首先安装 gitlab 
2. 然后安装gitlab-runner (强烈建议不要使用同一台服务器)
    * 此服务器首先必须安装docker 
    
## 第二部分 需要注意的事项：
### 2.1 runner 的注册：
> 参看文章： 
> 1. https://docs.gitlab.com.cn/runner/register/
> 2. https://docs.gitlab.com/runner/commands/#gitlab-runner-register

runner的注册的元素有以下部分： ( 具体的操作请看出 [参考文档 1](https://docs.gitlab.com.cn/runner/register/))
1. 运行下面命令启动注册程序:
2. 输入 GitLab 实例 URL:
3. 输入获取到的用于注册 Runner 的 token:
4. 输入该 Runner 的描述，稍后也可通过 GitLab's UI 修改:
5. 给该 Runner 指派 tags, 稍后也可以在 GitLab's UI 修改:
6. Enter the Runner executor: 这里推荐使用 docker 作为 executor
7. 如果你选择 Docker 作为你的 executor，注册程序会让你设置一个默认的镜像， 作用于 .gitlab-ci.yml 中未指定镜像的项目：

它的意义在于：
1. 创建一个runner、 这个runner 可以用来自动化地完成一些软件集成工作。

产生的结果：
1. 会创建一个runner ，并且runner 的配置信息会默认配置到某个配置文件，比如：`/etc/gitlab-runner/config.toml`
2. 一个配置文件可以记载多个runner；
3. GitLab-CI会为Runner生成一个唯一的token，以后Runner就通过这个token与GitLab-CI进行通信。

注意事项：
1. 一个机器可以创建多个runner, 这样可以让多个runner 分别异步同上完成不同的任务。
2. 不能直接修改配置文件来创建runner：
    * 因为它会发送请求给gitlab， 让他们建立连接；
    * 配置文件`.toml` 只是记录runner 的配置信息而已
3. 快捷注册：
    ```
    gitlab-runner register -u http://192.168.33.10/ -r jtvLcjb7EjksR7eLMZNQ  --executor docker --docker-image ubuntu:16.04  -c /etc/gitlab-runner/config-1.toml
    ```

**总结： 创建runner的时候个人建议是根据实际需求，把需要的异步执行的runner 分别创建在不同的配置文件下面， 因为后面会用得到（不同的配置文件应用于不同的service 中，这样可以达到 runner之间异步执行的效果）**

### 2.2 创建一个service， 并且运行它
首先注册一个runner 运行的service ，并且附带使用哪个配置文件、哪个用户、并且service 的名称 
```
gitlab-runner install  \
    --service gitlab-runner-1 \
    --config /etc/gitlab-runner/config-1.toml \
    --user root
```

其他部分（对服务器的重启、关闭等等执行查看手册）：
```
gitlab-runner -h
```