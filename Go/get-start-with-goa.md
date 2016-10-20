goa入门
==

#前提
安装`goa`和`goagen`：
```bash
go get -u github.com/goadesign/goa/...
```

#设计
编写goa服务的第一件事就是使用goa设计语言来描述API。在`$GOPATH/src`下为goa服务创建一个新目录，例如`$GOPATH/src/cellar`。在目录下新建一个`design`子目录和`design/design.go`并输入以下内容：
```go
package design                                     // The convention consists of naming the design
                                                   // package "design"
import (
        . "github.com/goadesign/goa/design"        // Use . imports to enable the DSL
        . "github.com/goadesign/goa/design/apidsl"
)

var _ = API("cellar", func() {                     // API defines the microservice endpoint and
        Title("The virtual wine cellar")           // other global properties. There should be one
        Description("A simple goa service")        // and exactly one API definition appearing in
        Scheme("http")                             // the design.
        Host("localhost:8080")
})

var _ = Resource("bottle", func() {                // Resources group related API endpoints
        BasePath("/bottles")                       // together. They map to REST resources for REST
        DefaultMedia(BottleMedia)                  // services.

        Action("show", func() {                    // Actions define a single API endpoint together
                Description("Get bottle by id")    // with its path, parameters (both path
                Routing(GET("/:bottleID"))         // parameters and querystring values) and payload
                Params(func() {                    // (shape of the request body).
                        Param("bottleID", Integer, "Bottle ID")
                })
                Response(OK)                       // Responses define the shape and status code
                Response(NotFound)                 // of HTTP responses.
        })
})

// BottleMedia defines the media type used to render bottles.
var BottleMedia = MediaType("application/vnd.goa.example.bottle+json", func() {
        Description("A bottle of wine")
        Attributes(func() {                         // Attributes define the media type shape.
                Attribute("id", Integer, "Unique bottle ID")
                Attribute("href", String, "API href for making requests on the bottle")
                Attribute("name", String, "Name of wine")
                Required("id", "href", "name")
        })
        View("default", func() {                    // View defines a rendering of the media type.
                Attribute("id")                     // Media types may have multiple views and must
                Attribute("href")                   // have a "default" view.
                Attribute("name")
        })
})
```
- 我们定义了一个名为`design`的包，并使用了一个匿名变量来声明API，也可以使用初始化的方法。包可以试用任何实际名字，此处使用`design`只为方便。
- 方法`API`对API进行了声明，并接受两个参数：API的名字和一个用来定义附加属性的匿名方法。在这个`cellar`的例子里，我们使用了`title`和`description`。
- 方法`Resource`声明了一个`bottle`资源，同时也接受了一个`name`和一个匿名方法。匿名方法中定义的属性包括所有被此 *资源* 支持的动作和一个用来在回复里渲染此 *资源* 默认的媒体类型。
- 资源的每一个动作使用`Action`来声明，`Action`同样使用`name`和一个匿名方法。动作被声明在资源内部，它们表示特定的由HTTP方法、URL、参数、有效负载和回复定义组成的API端点。参数可以使用URL内的通配符来进行定义，或者与URL里附加的查询字符串一一对应；有效负载描述了请求正文的数据结构。此处我们只定义了一个单独的动作`show`，但是资源可以定义任意数量的动作。
- `Action`方法定义了动作端点、参数、有效负载(此处未使用)和回复。goa为所有的标准HTTP状态代码定义了默认的回复模板。*回复模板* 定义了回复的HTTP状态代码、其媒体类型和头字段(`header`)。`ResponseTemplate`设计语言方法(此处未使用)可以用来定义额外的回复模板或覆盖现有的。
- 最后，我们把资源媒体类型(`resource media type`)定义为全局变量以便在声明`OK`回复时指向它。媒体类型包含一个由RFC 6838定义的识别码，并描述了回复正文的属性(goa中的JSON)对象。

#实现
在API设计完成后，我们就可以使用`goagen`工具来生成所有的样板代码了。`goagen`工具使用 *生成目标* 和 *Go设计包的导入路径* 作为参数。此处我们将要开启一个新的服务，所以我们使用`bootstrap`目标来生成一整套完全实现。如果你创建的设计包在目录`$GOPATH/src/cellar`里，你要使用的命令是：
```bash
goagen bootstrap -d cellar/design
```
工具会输出所生成的文件的名字 - 默认情况下会在 **当前路径里** 生成的文件。生成的列表会像如下这样：
```
app
app/contexts.go
app/controllers.go
app/hrefs.go
app/media_types.go
app/user_types.go
app/test
app/test/bottle.go
main.go
bottle.go
tool/cellar-cli
tool/cli
client
client
tool/cellar-cli/main.go
tool/cli/commands.go
client/client.go
client/bottle.go
client/datatypes.go
swagger
swagger/swagger.json
swagger/swagger.yaml
```
注意`goagen`是如何为我们的程序生成`main.go`和一个骨架控制器(`bottle.go`)。这两个文件的意图是帮助引导一个新的开发，如果这两个文件已经存在，那么(默认情况下)它们不会重新被生成(重新运行`goagen`工具，注意这次它只生成在目录`app`、`client`、`tool`和`swagger`下的文件)。这些行为以及其他方面可以通过命令行参数来进行设置，详情参见goagen文档。

回到生成的文件列表：

- `app`目录包含所生成的底层HTTP路由代码。
- `client`目录包含用来实现客户Go包?(`client Go package`)的代码。
- asd 























