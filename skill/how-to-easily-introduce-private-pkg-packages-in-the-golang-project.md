# Golang项目中如何轻松实现私有仓库pkg包的引入

在企业内部创建一个公共的Golang模块工程可以帮助提高代码复用性和开发效率。本文将从如何创建一个公共的Golang工程开始，指导你一步步创建它、并引入到你的工程中。

## 公共模块规范

下面是一个简单的步骤指南来创建这样一个公共模块项目。

创建版本控制仓库：使用版本控制工具（如Git）创建一个新的代码仓库，用于管理该公共模块工程的代码。

项目结构：定义好项目的结构，使其易于理解和使用。一个常见的Golang项目结构如下：

```shell
my-module/
   |- README.md        # 项目文档和说明
   |- go.mod           # Go模块定义文件
   |- go.sum           # Go模块的依赖版本文件
   |- cmd/             # 命令行应用程序目录（如果有的话）
   |- pkg/             # 项目的库代码目录
   |- internal/        # 内部库代码目录，不对外公开
   |- examples/        # 示例代码目录
   |- tests/           # 单元测试和集成测试目录

```

1. Go模块初始化：进入项目根目录，运行 `go mod init <module-name>` 来初始化 Go 模块。模块名称应该是该模块工程的唯一标识符。

2. 编写代码：在 `pkg/` 目录下编写公共的Golang库代码。这些代码应该是可复用的，不与具体应用逻辑绑定。

3. 文档和注释：对于公共模块，清晰的文档和注释是非常重要的。确保每个公共函数和类型都有良好的注释，方便其他开发人员理解和使用。

4. 示例代码：在 `examples/` 目录下提供一些简单的示例代码，展示如何使用这个公共模块。

5. 单元测试：编写完整的单元测试和集成测试，保证模块的正确性和稳定性。测试代码放在 `tests/` 目录下。

6. 版本管理：在Go中，版本管理使用Go Modules。当你的模块准备发布新版本时，确保适当地更新go.mod文件，并通过 `go get <module-name>@<version>` 或 `go mod tidy` 来更新依赖关系。

7. 持续集成：将公共模块纳入到企业的持续集成流程中，确保每次修改都通过了测试，并符合质量标准。

8. 发布和文档：根据企业内部的发布流程，发布新的版本，并及时更新项目文档，方便其他开发人员使用。

以上是一个基本的指南来创建企业内部的Golang公共模块工程，可根据实现项目灵活调整。记得在开发过程中关注代码质量和安全性，并积极倾听来自其他开发人员的反馈，不断改进和优化模块。

## 如何引入公共模块

在Go中引入私有Git仓库的包可以通过在 `go.mod` 文件中添加 `replace` 或 `require` 语句来实现，具体取决于你的使用场景。以下是引入私有Git仓库包的三种常见方法。

在下述前两种方法中，你需要将 `github.com/yourusername/yourprivatepkg` 替换为你私有Git仓库的实际路径，然后根据需要设置路径或版本号。

完成后，运行 `go mod tidy` 命令来更新依赖。Go会自动下载和管理你的私有Git仓库包。

### 私有项目的路径替换

使用 `replace` 语句实现。

如果你希望在开发过程中使用本地路径或其他方式替代私有Git仓库，可以使用 `replace` 语句。这样可以方便地在不同环境中进行开发和测试。

如果私有项目 A 依赖另一个私有项目 B，且通过 `go get` 无法获取权限，可以采用 `replace` 方式。

在你的项目的 `go.mod` 文件中添加类似如下的 `replace` 语句，将私有Git仓库的路径替换为本地路径或其他路径：

```go
replace github.com/yourusername/yourprivatepkg => /path/to/local/repo
```

或者，你可以使用相对路径：

```go
replace github.com/yourusername/yourprivatepkg => ../path/to/local/repo
```

### go mod私有项目的访问

使用 `require` 语句实现。

如果你想直接从私有Git仓库获取包并将其添加到项目依赖中，可以使用 `require` 语句。

在你的项目的 `go.mod` 文件中添加类似如下的 `require` 语句，将私有Git仓库的路径和版本号添加到依赖中：

```go
require github.com/yourusername/yourprivatepkg v1.0.0
```

要实现上述方式，需要额外的配置，确保能够正常 `go get` 私有Git仓库的工程。

1. 设置 Go GOPRIVATE 变量

   ```shell
   # 配置多个私有项目地址
   go env -w GOPRIVATE="gitlab.example.com"
   # 其中gitee.com/user 是你的个人账户所在地址
   ```

   * 默认情况下，如果设置GOPRIVATE，会自动设置GONOPROXY和GONOSUMDB配置；

   * Golang项目非代理NOPROXY配置
     如果设置GONOPROXY和GONOSUMDB均为none，意味着所有module，不管是公共的还是私有的，都要经过proxy下载，经过sumdb验证。

2. 私有仓库的请求认证

   方法一：在请求URL中嵌入认证信息

   ```shell
   git config --global url."https://${user}:${password}@gitlab.example.com".insteadOf "https://gitlab.example.com"
   ```

   Git全局配置查看和删除:

   ```shell
   #查看git全局配置
   git config --global -l
   # 删除url路径替换, 或是修改${GitProject}/.git/config文件中对应url
   git config --global --unset url."git@gitlab.example.com".insteadOf
   ```

   方法二：使用SSH替换HTTPS进行认证

   ```shell
   git config --global url."git@gitlab.example.com".insteadOf "https://gitlab.example.com"
   ```

3. Golang私有项目的http访问

   如果私有库不支持https协议，会报如下的错误。这是因为Go更新依赖时，会强制校验CA证书来确保依赖库的安全性。

   ```shell
   go: gitee.com/modules/project@v0.0.0-20200320063051-28c4ad7fe2ea: unrecognized import path "gitee.com/modules/project": https fetch: Get "https://gitee.com/modules/project?go-get=1": dial tcp 123.123.123:443: connect: connection refused
   ```

   如果私有库不支持https协议，还需要go配置参数或环境变量，使其使用http方式访问。

   方法一：go get -insecure

   使用 `go get -insecure`，这种方式不推荐，原因如下：

   * 添加 `-insecure`` 参数，即表示更新依赖时可以不去校验CA证书，但是这会带来一个问题：范围无法界定(overkill)，所有与要更新依赖相关联的依赖，均不会去做校验，可能会意外更新到不安全的依赖。

   * `-insecure` 仅支持 go get 命令，不支持 go mod 命令，因此使用 go mod 命令时是无法更新不支持https协议的私有库的。

   方法二：GOINSECURE

   添加 `GOINSECURE` 参数，**推荐这种方式**。

   * 在Go 1.14 中增加了新的环境变量，用于指定哪些域名下的仓库不去校验 CA 证书。

   * 使用方式同 `GOINSECURE` 类似 `go env -w GOINSECURE=gitlab.example.com`。

### git 子模块（推荐）

在 Go 中，你可以使用 Git 子模块的方式引用私有的公共模块工程，以便在你的项目中使用。

1. 创建 Git 子模块

   在你的项目中，使用以下命令添加 Git 子模块，将私有的公共模块工程作为子模块引入：

   ```bash
   git submodule add <repository-url> path/to/submodule
   ```

   其中 `<repository-url>` 是你私有模块工程的 Git 仓库 URL，`path/to/submodule` 是子模块在你的项目中的路径。

2. 配置认证

   如果你的私有模块工程需要身份验证，你可能需要在执行 `go get` 或 `go mod` 时提供适当的认证凭证。这可能包括用户名、密码、Personal Access Token（如果使用 GitLab）等。

3. 使用子模块的包

   在你的项目中，可以通过 `import` 语句引入子模块的包，然后在代码中使用这些包。

   ```go
   import (
       "your/repo/path/to/submodule/package"
   )

   func main() {
       // 使用子模块的包
       submodulepackage.DoSomething()
   }
   ```

4. 使用 Go Modules 进行版本管理

   确保你的项目启用了 Go Modules，并在 `go.mod` 文件中添加了子模块的引用。

   ```bash
   go mod edit -replace=example.com/submodule=path/to/submodule
   ```

5. 更新子模块

   如果子模块的工程代码发生变化，你可以进入子模块目录，使用 `git pull` 更新子模块的代码。然后在你的项目根目录使用 `go mod tidy` 更新模块依赖。

## 总结

条条道路通罗马，技术更是如此。

具体选择那种取决于你的实际环境、实际场景，这里只提供一个思路、方向供大家参考。

---

参考文章：

1. [Go Mod引用私有库](https://turbock79.cn/?p=3570)
2. [怎么让Go Modules使用私有依赖模块](https://cloud.tencent.com/developer/article/1871793)
3. [Git: submodule 子模块简明教程](https://iphysresearch.github.io/blog/post/programing/git/git_submodule/)