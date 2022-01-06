# 配置 Git Commit Message 规范提交信息

## 目的

- 提高代码可维护性
- 自动化生成 Changelog
- 根据提交类型分类

## 安装依赖

1. 安装 commitizen 替代 `git commit`

```
  $ npm install -g commitizen
```

2. 在项目目录里执行如下命令，使其支持 Angular 的 Commit message 格式

```
  $ commitizen init cz-conventional-changelog --save --save-exact
```

&emsp;&emsp;配置 package.json

```cz常规changelog的配置
{
    "scripts": {
        "commit": "git-cz"
    },
    "config": {
        "commitizen": {
          "path": "node_modules/cz-conventional-changelog"
        }
    }
}
```

3. standard-version 如果我们想自动生成 CHANGELOG，语义化我们的版本[（Semantic Versioning）](https://semver.org/lang/zh-CN/)[[1]](#link1)。 就需要借助 [standard-version](https://github.com/conventional-changelog/standard-version)[[2]](#link2)

```
  $ npm install standard-version -D
```

&emsp;&emsp;配置 package.json

```
{
    "script": {
        // .....
        "release": "standard-version"
    }
}
```

&emsp;&emsp;关于 release:

```
  // 发布首个版本
  npm run release -- --first-release

  // 发布预发布版本
  // 例如：v1.0.0 -> v1.0.0-0
  npm run release -- --prerelease

  // 发布与首个 alpha 版本
  // 例如：v1.0.0 -> 1.0.1-alpha.0
  npm run release -- --prerelease alpha

  // 发布 major、minor、patch 版本
  npm run release -- --release-as minor
```

4. 以后，凡是用到 `git commit` 命令，一律改为使用 `git cz` 。这时，就会出现选项，用来生成符合格式的 Commit message

```
  1.Select the type of change that you're committing 选择改动类型 (<type>)

  2.What is the scope of this change (e.g. component or file name)? 填写改动范围 (<scope>)

  3.Write a short, imperative tense description of the change: 写一个精简的描述 (<subject>)

  4.Provide a longer description of the change: (press enter to skip) 对于改动写一段长描述 (<body>)

  5.Are there any breaking changes? (y/n) 是破坏性修改吗？默认n (<footer>)

  6.Does this change affect any openreve issues? (y/n) 改动修复了哪个问题？默认n (<footer>)
```

## 参考链接

<span name="link1">[1]</span> [Semantic Versioning](https://semver.org/lang/zh-CN/)

<span name="link2">[2]</span> [Commit message 和 Change log 编写指南](https://github.com/conventional-changelog/standard-version)

<span name="link3">[3]</span> [如何配置 Git Commit Message](https://zhuanlan.zhihu.com/p/69635847)

<span name="link4">[4]</span> [Commit message 和 Change log 编写指南](https://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html)
