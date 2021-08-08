# Gin 路由

Gin 是一个标准的 Web 服务框架，遵循 Restful API 接口规范，其路由库是基于 httproute 实现的。

本节将从 Gin 路由开始，详细讲述各种路由场景下，如何通过 Gin 来实现。

## 基本路由

Gin 支持 GET、POST、PUT、PATCH、DELETE、OPTIONS 等请求类型。

[示例](https://github.com/xcbeyond/golangLearning/tree/main/framework/gin-demo/route/basic/basic-route.go)：

```go
package main

import (
	"net/http"

	"github.com/gin-gonic/gin"
)

func main() {
	route := gin.Default()

	// 设置一个get请求，其URL为/hello，并实现简单的响应
	route.GET("/get", func(c *gin.Context) {
		c.JSON(http.StatusOK, gin.H{
			"message": "this is a get method response!",
		})
	})

	// 具体实现可单独定义一个函数
	route.POST("/post", postHandler)

	route.PUT("/put", func(c *gin.Context) {

	})

	route.PATCH("/patch", func(c *gin.Context) {

	})

	route.DELETE("/delete", func(c *gin.Context) {

	})

	// ……

	route.Run()
}

func postHandler(c *gin.Context) {
	c.JSON(http.StatusOK, "this is a post method response!")
}
```

## 参数处理

### path 参数

可通过 `*gin.Context` 的 Param 函数获取请求 Path 中的参数，Path 路径中参数以`:`开头，如：`/user/:name`，能够匹配路径 `/user/xx`。

[示例](https://github.com/xcbeyond/golangLearning/tree/main/framework/gin-demo/route/param/request-param.go)：

```go
package main

import (
	"fmt"
	"net/http"

	"github.com/gin-gonic/gin"
)

type User struct {
	Username string   `json:"username"`
	Sex      string   `json:"sex"`
	Age      int      `json:"age"`
	Labels   []string `json:"lalels"`
}

func main() {
	route := gin.Default()

	users := []User{
		{
			Username: "xcbeyond",
			Sex:      "男",
			Age:      18,
			Labels:   []string{"年轻", "帅"},
		},
	}

	// 请求path中存在参数
	route.GET("/user/:name", func(c *gin.Context) {
		// 获取请求path中的参数
		name := c.Param("name")
		for _, user := range users {
			if user.Username == name {
				c.JSON(http.StatusOK, user)
				return
			}
		}
		c.JSON(http.StatusOK, fmt.Errorf("not found user [%s]", name))
	})

	route.Run()
}

```

### 查询参数

可通过 `*gin.Context` 的 Query 函数获取参数，如：`/user?name=xcbeyond`。

[示例](https://github.com/xcbeyond/golangLearning/tree/main/framework/gin-demo/route/param/request-param.go)：

```go
package main

import (
	"net/http"

	"github.com/gin-gonic/gin"
)

type User struct {
	Username string   `json:"username"`
	Sex      string   `json:"sex"`
	Age      int      `json:"age"`
	Labels   []string `json:"lalels"`
}

func main() {
	route := gin.Default()

	users := []User{
		{
			Username: "xcbeyond",
			Sex:      "男",
			Age:      18,
			Labels:   []string{"年轻", "帅"},
		},
	}

	// 查询参数，如：/user?name=xcbeyond
	route.GET("/user", func(c *gin.Context) {
		// 获取中的参数
		name := c.Query("name")
		for _, user := range users {
			if user.Username == name {
				c.JSON(http.StatusOK, user)
				return
			}
		}
		c.JSON(http.StatusOK, "not found user "+name)
	})

	route.Run()
}

```

### ……

## 路由分组

Gin 提供了路由分组的能力，方便管理分组管理路由，将具有相同路由 URL 前缀的进行分类处理，常见于不同版本的分组，如：`/api/v1`、`/api/v1`。

此外，还支持多层分组。

[示例](https://github.com/xcbeyond/golangLearning/tree/main/framework/gin-demo/route/group/group-route.go)：

```go
package main

import (
	"math/rand"
	"net/http"

	"github.com/gin-gonic/gin"
)

type User struct {
	Username string   `json:"username"`
	Sex      string   `json:"sex"`
	Age      int      `json:"age"`
	Labels   []string `json:"lalels"`
}

func main() {
	route := gin.Default()

	users := []User{
		{
			Username: "xcbeyond",
			Sex:      "男",
			Age:      18,
			Labels:   []string{"年轻", "帅"},
		},
		{
			Username: "niki",
			Sex:      "女",
			Age:      16,
			Labels:   []string{"漂亮"},
		},
	}

	// api分组
	api := route.Group("/api")
	{
		// v1分组
		v1 := api.Group("/v1")
		{
			v1.GET("/user/:name", func(c *gin.Context) {
				name := c.Param("name")
				for _, user := range users {
					if user.Username == name {
						c.JSON(http.StatusOK, user)
						return
					}
				}
				c.JSON(http.StatusOK, "not found user :"+name)
			})
		}

		// v2分组
		v2 := api.Group("/v2")
		{
			v2.GET("/user/:name", func(c *gin.Context) {
				name := c.Param("name")
				for _, user := range users {
					if user.Username == name {
						c.JSON(http.StatusOK, user)
						return
					}
				}

				// 如果没有查到，则随机返回一个
				user := users[rand.Intn(len(users)-1)]
				c.JSON(http.StatusOK, "not found user ["+name+"],but user ["+user.Username+"] is exist!")
			})
		}
	}

	route.Run()
}

```

## 路由拆分

上述路由示例中，都是将所有路由信息写在同一个源文件、函数中，但在实际项目中会涉及大量的接口，写在一起就显得太不合适了。

在实际项目中，我们更倾向于将路由代码拆分出来，可拆分为单独的包、多个路由源文件等。

根据实际项目的规模大小，可分不同粒度拆分。

（以下路由拆分仅供参考，可根据具体项目灵活调整！）

### 路由拆分为单独源文件或包

将路由实现分离到单独的包下，使得项目结构更加清晰。项目结构如下：

```sh
.
├── main.go
└── routes
    └── routes.go
```

示例完整源码：[route-split-v1](https://github.com/xcbeyond/golangLearning/tree/main/framework/gin-demo/route/route-split/split-v1)

在 `/routes/routes.go` 文件中实现并注册路由信息：

```go
package routes

import (
	"net/http"

	"github.com/gin-gonic/gin"
)

type User struct {
	Username string   `json:"username"`
	Sex      string   `json:"sex"`
	Age      int      `json:"age"`
	Labels   []string `json:"lalels"`
}

var users []User

// 为方便测试，则直接通过init方式赋值。实际项目中，一般通过数据库等其它方式查询获取数据。
func init() {
	users = []User{
		{
			Username: "xcbeyond",
			Sex:      "男",
			Age:      18,
			Labels:   []string{"年轻", "帅"},
		},
		{
			Username: "niki",
			Sex:      "女",
			Age:      16,
			Labels:   []string{"漂亮"},
		},
	}
}

// SetupRouter 配置路由
func SetupRouter() *gin.Engine {
	route := gin.Default()

	route.GET("/user/:name", querUserHandler)
	// 其它更多路由

	return route
}

// handler
func querUserHandler(c *gin.Context) {
	name := c.Param("name")
	for _, user := range users {
		if user.Username == name {
			c.JSON(http.StatusOK, user)
			return
		}
	}
	c.JSON(http.StatusOK, "not found user :"+name)
}

```

并在 main.go 中调用路由配置函数 SetupRouter 即可：

```go
func main() {
	route := routes.SetupRouter()
	if err := route.Run(); err != nil {
		fmt.Printf("startup server failed,err: %v", err)
	}
}
```

### 路由拆分为多个源文件

当随着项目的业务功能丰富，体量变大，所有的路由都写在一个 routes.go 源文件中，将会导致这个源文件越来越臃肿，不便于后期维护和阅读。

因此，可根据某种维度拆分为多个路由文件来实现不同业务功能。项目结构如下：

```sh
.
├── main.go
└── routes
    ├── auth.go
    ├── routes.go
    └── user.go
```

示例完整源码：[route-split-v2](https://github.com/xcbeyond/golangLearning/tree/main/framework/gin-demo/route/route-split/split-v2)

在 routes 包下，根据某种维度拆分为多个路由实现文件，如：根据业务模块，可拆分为认证模块（auth.go）、用户模块（user.go）等，并在各自路由文件中实现具体的业务功能，并进行路由注册。

如，认证模块 auth.go：

```go
package routes

import (
	"github.com/gin-gonic/gin"
)

// AuthRegister route register
func AuthRegister(e *gin.Engine) {
	e.GET("/auth/login", loginHandler)
	e.POST("/auth/logout", logoutUserHanler)
	// ……
}

// loginHandler
func loginHandler(c *gin.Context) {

}

// logoutUserHanler
func logoutUserHanler(c *gin.Context) {

}

```

其中，定义 AuthRegister 函数将该模块下所有路由进行注册，注意该函数为大写字母开头，作为全局函数能够被包外 main.go 调用。

routes/routes.go 中，配置路由，并统一注册所有模块的路由：

```go
package routes

import (
	"github.com/gin-gonic/gin"
)

// SetupRouter 配置路由
func SetupRouter() *gin.Engine {
	route := gin.Default()

	// other config

	// register all route.
	UserRegister(route)
	AuthRegister(route)
	// ……

	return route
}

```

main.go 和上一版本一样，作为程序等入口：

```go
func main() {
	route := routes.SetupRouter()
	if err := route.Run(); err != nil {
		fmt.Printf("startup server failed,err: %v", err)
	}
}
```

---

参考资料：

1. [api-examples](https://github.com/gin-gonic/gin#api-examples)
2. [gin框架路由拆分与注册](https://www.liwenzhou.com/posts/Go/gin_routes_registry/)
