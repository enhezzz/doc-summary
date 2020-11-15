#	构建阶段：部署到npm

这个例子有两个构建阶段：

- 四个分别对4-7的node版本运行测试的作业
- 一个部署包到npm的作业

下面是`.travis.yml`看起来的大致样子：

```yaml
language: node_js
node_js:
  - "7"
  - "6"
  - "5"
  - "4"

script: echo "Running tests against $(node -v) ..."

jobs:
  include:
    - stage: npm release
      node_js: "7"
      script: echo "Deploying to npm ..."
      deploy:
        provider: npm
        api_key: $NPM_API_KEY
        on: deploy-npm-release
```

矩阵可能看起来：

![](..\img\buildStage\npmDeploy.png)