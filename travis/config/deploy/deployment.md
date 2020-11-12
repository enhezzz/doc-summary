# 部署 (v2)

> 这里只做v2版本的介绍。查看[v1](https://docs.travis-ci.com/user/deployment)

##	如何选择v2版本[#](#how-to-opt-in-to-v2)

​	为了使用我们的部署工具的v2版本，请将以下内容添加到您的.travis.yml中：

```yam
deploy:
  provider: <provider>
  # ⋮
  edge: true
```

##	支持的部署提供商(provider)[#](#supported-providers)

​	支持连续部署到以下目标：

- ​	[NPM](./npmDeployment.md)
- [GITHUB PAGES](#)
- ...

这里只讲NPM。

##	拉取请求(pr)[#](#pull-requests)

​	请注意，拉取请求构建完全跳过了部署步骤。

##	成熟度等级[#](#maturity-levels)

​	为了传达当前对于一个特定服务[`dql`](https://github.com/travis-ci/dpl)（一个部署工具）的支持开发状态和成熟度，根据给定标准，的各个部署提供商会被标记为下面之一的成熟度等级：

- `dev`	-该提供商在开发过程中
- `alpha` -该提供商完全测试过了
- `beta`  -该提供商已经在`alpha`阶段至少一个月了并且已有生产部署被发现
- `stable`  -该提供商已经在`beta`阶段至少两个月了并且没有出现重大问题（像部署失败，文档记录的功能故障）

**Dpl v2代表主要重写，因此已根据测试状态将对所有部署提供商的支持重置为dev或alpha。**

##	保护私密信息[#](#securing-secrets)

​	机密选项值应在构建配置（.travis.yml文件）中配置成加密字符串或在travis存储库设置中作为环境变量给出。

​	可以在travis存储库的设置页面上设置环境变量，或使用`travis env set`：

```yaml
// 当然肯定是要先登录的
travis env set <PROVIDER_NAME>_PASSWORD <password>
```

为了在将选项值添加到`.travis.yml`文件中时对其进行加密，请使用`travis encrypt`：

```yaml
travis encrypt <password>
```

##	有条件的部署[#](#conditional-deploys)

​	您可以只满足某些条件时进行部署。看[条件部署](./conditionalDeployment.md)

##	清理Git工作目录[#](#cleaning-up-the-git-working-directory)

​	默认情况下，在部署步骤之前不会清理您的Git工作目录，因此它可能包含先前步骤中剩余的产物。

​	如果确实需要清除从构建过程中工作目录产生的的所有更改，则可以将以下内容添加到`.travis.yml`文件中：

```yaml
deploy:
  provider: [your provider]
  cleanup: true
```

##	在部署步骤之前和之后运行命令[#](#running-commands-before-and-after-the-deploy-step)

```yaml
before_deploy: "echo ready?"
deploy:
  # ⋮
after_deploy:
  - ./after_deploy_1.sh
  - ./after_deploy_2.sh
```

##	部署到多个目标[#](#deploying-to-multiple-targets)

```yaml
deploy:
  - provider: s3
    access_key_id: <AWS access key id>
    secret_access_key: <AWS secret access key>
    # ⋮
  - provider: heroku
    api_key: <Heroku api key>
    # ⋮
```

