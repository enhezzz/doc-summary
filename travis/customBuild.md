# 自定义构建

## 构建配置

​	项目根目录下有一个.travis.yml文件，这是ci配置文件，用作主配置文件。

​	advance:  主配置文件可以import其他共享的配置文件。

## 构建超时

​	略

## 构建声明周期

​	[构建声明周期](./buildLifeCycle.md)

## 限制并发任务



## 禁止git clone



## 构建指定分支

​	当在有.travis.yml文件的分支下提交时会触发构建

###    包含或移除分支

```yml
# blocklist
branches:
  except:
  - legacy
  - experimental

# safelist
branches:
  only:
  - master
  - stable
```



