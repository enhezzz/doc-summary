# Job Lifecycle(作业生命周期)

> [原文](https://docs.travis-ci.com/user/job-lifecycle/)

Travis CI为每种编程语言提供了默认的构建环境和默认的阶段。在任务中拥有相应构建环境的虚拟机将被创建，你的仓库将会被clone到里面，可以安装可选的插件，接着构建阶段就开始了。

##	构建[#](#the-build)

​	.travis.yml文件描述了构建过程。 Travis CI中的构建是一系列[stages]()。在作业中的每个stage是并行运行的。

##	作业生命周期[#](#the-job-lifecycle)

​	每个作业中包含一系列阶段。主要阶段是：

1. `install`  -安装所有所需依赖
2. `script` -运行构建脚本

Travis CI可以在以下阶段中运行自定义命令：

1. `before_install` -在`install`阶段前
2. `before_script`  -在`script`阶段前
3. `after_script` -在`script`阶段后
4. `after_success` -当构建成功后，构建结果在`TRAVIS_TEST_RESULT`环境变量中
5. `after_failure` -当构建失败后，构建结果在`TRAVIS_TEST_RESULT`环境变量中

有三个可选的部署阶段。

一个作业的完整阶段就是生命周期。这些阶段的顺序是：

1. （可选的）安装`apt addons`
2. （可选的）安装`cache components`
3. `before_install`
4. `install`
5. `before_script`
6. `script`
7. （可选的）`before_cache`（如果缓存是有效的）
8. `after_success`或者`after_failure`
9. （可选的）`before_deploy`（如果启用了部署）
10. （可选的）`deploy`
11. （可选的）`after_deploy`（如果启用了部署）
12. `after_script`

**一次构建可以包含许多作业**

##	自定义安装阶段[#](#customizing-the-installation-phase)

​	默认的依赖项安装命令取决于项目语言。例如，Java构建使用Maven还是Gradle，具体取决于存储库中存在哪个构建文件。当存储库中存在Gemfile时，Ruby项目将使用Bundler。

您可以指定自己的脚本来安装项目依赖项：

```yaml
install: ./install-dependencies.sh
```

使用自定义脚本时，它们应该是可执行的（例如，使用chmod + x），并且包含有效的shebang，例如/ usr / bin / env sh，/usr/bin/env ruby或/usr /bin /env python 。

您还可以提供多个步骤，例如安装ruby和节点依赖项：

```yaml
install:
  - bundle install --path vendor/bundle
  - npm install
```

当安装中的步骤之一失败时，构建将立即停止并标记为错误。

###		跳过安装阶段[#](#skipping-the-installation-phase)

​		通过将以下内容添加到您的.travis.yml中，完全跳过安装步骤：

```yaml
install: skip
```

##	自定义构建阶段[#](#customizing-the-build-phase)

​	默认的构建命令取决于项目语言。Ruby项目使用rake。

​	您可以覆盖.travis.yml中的默认构建步骤：

```yaml
script: bundle exec thor build
```

​	您还可以指定多个脚本命令：

```yaml
script:
- bundle exec rake build
- bundle exec rake builddoc
```

​	当其中一个构建命令返回非零退出代码时，Travis CI构建也会运行后续命令并累积构建结果。

​	在上面的示例中，如果bundle exec rake build返回的退出代码为1，则以下命令bundle exec rake builddoc仍会运行，但是该构建将导致失败。

​	如果第一步是运行单元测试，然后是集成测试，则您可能仍想查看单元测试失败时集成测试是否成功。

​	您可以通过使用一些Shell Magic来运行所有命令来更改此行为，但是当第一个命令返回非零退出代码时，构建仍然失败。

```yaml
script: bundle exec rake build && bundle exec rake builddoc
```

​	当bundle exec rake build失败时，此示例（请注意&&）立即失败。

###	注意$?[#](#customizing-the-build-phase)

​	脚本中的每个命令均由特殊的bash函数处理。此函数操纵$？生成适合显示的日志。因此，您不应该在脚本部分依赖$？的值去更改构建行为。有关更多技术讨论，请参见此[GitHub  issue](https://github.com/travis-ci/travis-ci/issues/3771)。

###	复杂的构建命令[#](#complex-build-commands)

​	如果具有很难在.travis.yml中进行配置的复杂构建环境，请考虑将这些步骤移至单独的Shell脚本中。该脚本可以是您存储库的一部分，并且可以从.travis.yml中轻松调用。

​	考虑一个您想运行更复杂的测试的方案，但只针对不是来自拉取请求的构建。 Shell脚本可能是：

```shell
#!/bin/bash
set -ev
bundle exec rake:units
if [ "${TRAVIS_PULL_REQUEST}" = "false" ]; then
  bundle exec rake test:integration
fi
```

​	**注意顶部的```set -ev```。 -e标志使脚本在一个命令返回非零退出代码后立即退出。如果您想尽早退出任何脚本可以很方便。它还有助于复杂的安装脚本，在该脚本中，一个失败的命令不会导致安装失败。**

​	-v标志使shell在执行脚本之前先打印对应行，这有助于确定哪些步骤失败了。

要想在`.travis.yml`运行shell脚本：

1. 将其仓库`scripts/run-tests.sh`中
2. 通过运行`chmod ugo + x scripts / run-tests.sh`使它可执行。
3. 提交到您的仓库。
4. 将其添加到您的.travis.yml：

```yaml
script: ./scripts/run-tests.sh
```

**为什么不应该在构建步骤中使用`exit`**

​	在作业生命周期中指定的步骤将被编译成单个bash脚本并且在worker进程中执行。

​	当要覆盖这些步骤时，不要使用`exit`shell 内置命令。如果这样做会冒着终止构建过程的风险，而不会给Travis提供执行后续任务的机会。

​	在自定义脚本中使用exit是安全的。如果出现一个错误，则任务将被标记为失败。

##	终止构建[#](#breaking-the-build)

​	如果作业生命周期的前四个阶段中的任何命令返回非零退出代码，则构建将被破坏：

- 如果`before_install`，`install`或`before_script`返回非零退出代码，则该构建错误并立即停止。
- 如果`script`返回一个非零的退出代码，则构建会失败，但是在标记为失败之前会继续运行。

`after_success`，`after_failure`，`after_script`，`after_deploy`和后续阶段的退出代码不影响构建结果。但是，如果这些阶段之一超时，则将构建标记为失败。

##	部署代码[#](#deploying-your-code)

​	部署是作业生命周期中的一个可选阶段。通过使用我们的持续部署提供商之一将代码部署到Heroku，Amazon或其他受支持的平台来定义此阶段。如果构建已终止，则跳过部署步骤。

​	在将文件部署到部署提供商时，通过将`skip_cleanup`添加到`.travis.yml`中，防止Travis CI重置工作目录并删除在构建过程中所做的所有更改（`git stash --all`）：

```yaml
deploy:
  skip_cleanup: true
```

您可以使用`before_deploy`阶段在部署之前运行命令。此阶段中的非零退出代码会将构建标记为错误。

如果您希望在部署后执行任何步骤，则可以使用`after_deploy`阶段。