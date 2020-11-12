# 有条件的部署

使用on选项。

```yaml
deploy:
  provider: <provider>
  # ⋮
  on:
    branch: release
    condition: $MY_ENV = value
```

如果满足on部分中指定的所有条件，则将部署您的构建。

可以使用以下条件：

##	repo[#](#repo)

​	要仅在构建发生在特定存储库上时进行部署，请以`owner_name / repo_name`格式添加`repo`：

```yaml
deploy:
  provider: <provider>
  # ⋮
  on:
    repo: travis-ci/dpl
```

##	Branch[#](#branch)

​	默认情况下，部署将仅在`master`分支上进行。您可以使用`branch`和`all_branches`选项覆盖它。

​	例如，仅在`production`分支上部署，请使用：

```yaml
deploy:
  provider: <provider>
  # ⋮
  on:
    branch: production
```

​	为了所有分支可部署：

```yaml
deploy:
  provider: <provider>
  # ⋮
  on:
    all_branches: true
```

##	Condition[#](#condition)

​	您可以指定单个Bash条件，该条件需要评估为true才能进行部署。

​	这必须是一个字符串值，这将被包装到`if [[ <condition> ]]; then <deploy>; fi`

​	例如，为了仅在`$ CC=gcc`的情况下进行部署，请使用：

```yaml
deploy:
  provider: s3
  # ⋮
  on:
    condition: $CC = gcc
```

##	Tag[#](#tag)

​	您可以使用选项标签指定是否在标签构建上进行部署。如果设置为true，则部署将仅在标记构建中进行；同理，false相反。

##	有条件部署的示例[#](#examples-for-conditional-deployments)

​	这将使用自定义脚本deploy.sh进行部署，仅用于在`staging`和`staging`分支的构建中。

```yaml
deploy:
  provider: script
  script: deploy.sh
  on:
    all_branches: true
    condition: $TRAVIS_BRANCH =~ ^(staging|production)$
```

这将根据触发作业的分支使用自定义脚本`deploy_production.sh`和`deploy_staging.sh`进行部署。

```yaml
deploy:
  - provider: script
    script: deploy_production.sh
    on:
      branch: production
  - provider: script
    script: deploy_staging.sh
    on:
      branch: staging
```

仅在`$ CC=gcc`时，才部署到S3

```yaml
deploy:
  provider: s3
  access_key_id: <encrypted access_key_id>
  secret_access_key: <encrypted secret_access_key>
  bucket: <bucket>
  on:
    condition: $CC = gcc
```

