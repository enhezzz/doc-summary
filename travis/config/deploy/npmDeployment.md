# NPM 发布

成功构建后，Travis CI可以自动发布到NPM或另一个类似npm的registry。

对于最低配置，请在.travis.yml中添加以下内容：

```yaml
deploy:
  provider: npm
  api_token: <encrypted api_token>
  edge: true # opt in to dpl v2
```

##	已知选项[#](#status)

​	使用以下选项进一步配置部署。

​	`email`		npm account email 

​	`api_token`	npm api token — **必填**, **私密的**,注意：可以从本地〜/ .npmrc文件中检索

​	`access`	访问权限,已知值：`public`, `private`

​	`registry`		npm registry url

​	`src`		发布的目录或压缩包，默认: `.`

​	`tag`	要添加的发布标签

​	`run_script`	从package.json运行给定的脚本，注意：跳过运行`npm publish`

​	`dry_run`	执行测试运行而不上传到注册表 -type: boolean

​	`auth_method`	身份验证方法, 已知值：`auth`

###			共享选项[#](#shared-options)

​		`cleanup`	部署前从Git工作目录中清理构建工产物	-type: boolean

​		`run`	部署成功完成后要执行的命令	-type: string or array of strings

##	环境变量[#](#environment-variables)

​	如果前缀为NPM_，则所有选项都可以作为环境变量给出。

​	例如，api_token可以指定为$NPM_API_TOKEN

##	标记发布[#](#tagging-releases)

​	您可以使用标签选项自动添加npm发布标签：

```yaml
deploy:
  # ⋮
  tag: next
```

##	注意.gitignore[#](#note-on-gitignore)

​	请注意，如果.npmignore不存在，则npm部署会遵循.gitignore。这意味着，如果您的构建产物在.gitignore中列出的位置中，则它们将不会包含在上传的包中。

##	解决“ npm ERR！您需要一个付费帐户才能执行此操作。”[#](#troubleshooting-npm-err-you-need-a-paid-account-to-perform-this-action)

​	`npm`假定默认情况下作用域包是私有的。您可以显式告诉npm您的软件包是公共软件包，并通过将以下内容添加到`package.json`文件来避免此错误：

##	部署标签[#](#deploying-tags)

​	最有可能的是，您只想在有新版本的软件包时进行部署。

​	为此，您可以添加一个标签条件，如下所示：

```yaml
deploy:
  provider: npm
  # ⋮
  on:
    tags: true
```

如果您在本地标记提交，请记住运行`git push --tags`以确保您的标记已上传到GitHub。



##	拉取请求[#](#pull-requests)

​	请注意，拉取请求构建完全跳过了部署步骤。

