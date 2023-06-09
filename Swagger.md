# 简介

前后端相对独立，松耦合；

接口管理

最流行的API框架

restful API文档在线自动生成工具

https://editor.swagger.io/

使用需要导入依赖

```xml
<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger2 -->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>3.</version>
        </dependency>
<!-- 视图依赖，可以用眼睛演的到的 -->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>3.0.0</version>
        </dependency>
```

# 常用注解

@Api：用在controller类，描述API接口
@ApiOperation：描述接口方法
@ApiModel：描述对象
@ApiModelProperty：描述对象属性
@ApiImplicitParams：描述接口参数
@ApiResponses：描述接口响应
@ApiIgnore：忽略接口方法



常用注解：
- @Api()用于类；
  表示标识这个类是swagger的资源

- @ApiOperation()用于方法；
  表示一个http请求的操作

- @ApiParam()用于方法，参数，字段说明；
  表示对参数的添加元数据（说明或是否必填等）

- @ApiModel()用于类
  表示对类进行说明，用于参数用实体类接收

- @ApiModelProperty()用于方法，字段
  表示对model属性的说明或者数据操作更改

- @ApiIgnore()用于类，方法，方法参数
  表示这个方法或者类被忽略

- @ApiImplicitParam() 用于方法
  表示单独的请求参数

- @ApiImplicitParams() 用于方法，包含多个 @ApiImplicitParam

  # 具体使用举例说明：

  ## @Api()

  用于类；表示标识这个类是swagger的资源
  tags–表示说明
  value–也是说明，可以使用tags替代
  但是tags如果有多个值，会生成多个list

  ```Java
  @Api(value="用户controller",tags={"用户操作接口"})
  @RestController
  public class UserController {
  
  }
  
  ```

  ## @ApiOperation() 用于方法；表示一个http请求的操作

  value用于方法描述
  notes用于提示内容
  tags可以重新分组（视情况而用）
  @ApiParam() 用于方法，参数，字段说明；表示对参数的添加元数据（说明或是否必填等）
  name–参数名
  value–参数说明
  required–是否必填

  ```Java
  @Api(value="用户controller",tags={"用户操作接口"})
  @RestController
  public class UserController {
       @ApiOperation(value="获取用户信息",tags={"获取用户信息copy"},notes="注意问题点")
       @GetMapping("/getUserInfo")
       public User getUserInfo(@ApiParam(name="id",value="用户id",required=true) Long id,@ApiParam(name="username",value="用户名") String username) {
       // userService可忽略，是业务逻辑
        User user = userService.getUserInfo();
    return user;
    }
  }
  ```

  ## @ApiModel()用于类 ；表示对类进行说明，用于参数用实体类接收

  value–表示对象名
  description–描述
  都可省略

  ## @ApiModelProperty()用于方法，字段； 表示对model属性的说明或者数据操作更改

  value–字段说明
  name–重写属性名字
  dataType–重写属性类型
  required–是否必填
  example–举例说明
  hidden–隐藏

  ```java 
  @ApiModel(value="user对象",description="用户对象user")
  public class User implements Serializable{
      private static final long serialVersionUID = 1L;
       @ApiModelProperty(value="用户名",name="username",example="xingguo")
       private String username;
       @ApiModelProperty(value="状态",name="state",required=true)
        private Integer state;
        private String password;
        private String nickName;
        private Integer isDeleted;
  ```

  ```java
    @ApiModelProperty(value="id数组",hidden=true)
    private String[] ids;
    private List<String> idList;
   //省略get/set
  ```
  ```java
    @ApiOperation("更改用户信息")
    @PostMapping("/updateUserInfo")
    public int updateUserInfo(@RequestBody @ApiParam(name="用户对象",value="传入json格式",required=true) User user){
   int num = userService.updateUserInfo(user);
   return num;
   }
  ```
   


  @ApiIgnore()用于类或者方法上，可以不被swagger显示在页面上
  比较简单, 这里不做举例

  @ApiImplicitParam() 用于方法
  表示单独的请求参数
  @ApiImplicitParams() 用于方法，包含多个 @ApiImplicitParam
  name–参数ming
  value–参数说明
  dataType–数据类型
  paramType–参数类型
  example–举例说明

  ```java
    @ApiOperation("查询测试")
    @GetMapping("select")
    //@ApiImplicitParam(name="name",value="用户名",dataType="String", paramType = "query")
    @ApiImplicitParams({
    @ApiImplicitParam(name="name",value="用户名",dataType="string", paramType = "query",example="xingguo"),
    @ApiImplicitParam(name="id",value="用户id",dataType="long", paramType = "query")})
    public void select(){
  
    }
  ```

  

  
  
