# 引入jar包

```xml
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.9.2</version>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.9.2</version>
        </dependency>
        <dependency>
            <groupId>com.google.guava</groupId>
            <artifactId>guava</artifactId>
            <version>28.0-jre</version>
        </dependency>
```

注意对guava的依赖

# swagger配置类

```java
import io.swagger.annotations.Api;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

@Configuration
@EnableSwagger2
public class Swagger2Configuration {

    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.withClassAnnotation(Api.class))//这是注意的代码
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("xxx接口文档")
                .description("xxx相关接口的文档")
                .termsOfServiceUrl("http://www.xxx.com")
                .version("1.0")
                .build();
    }

}
```

# 加注解

```java
@Api(value = "AdminUserController ")
@RestController
@RequestMapping("/admin/user")
public class AdminUserController extends BaseController {
    /**
     * 用户登录
     */
    @ApiOperation(value = "登录")
    @ApiImplicitParams({@ApiImplicitParam(name = "userName", value = "用户名", required = true, dataType = "String"),
            @ApiImplicitParam(name = "password", value = "密码", required = true, dataType = "String")})
    @PostMapping("/login")
    @SystemControllerLog(description = "/admin/user/login")
    public ResultVO login(String userName, String password){}
```

# 常用注解
* @Api： 描述 Controller
* @ApiIgnore： 忽略该 Controller，指不对当前类做扫描
* @ApiOperation： 描述 Controller类中的 method接口
* @ApiParam： 单个参数描述，与 @ApiImplicitParam不同的是，他是写在参数左侧的。如（ @ApiParam(name="username",value="用户名")Stringusername）
* @ApiModel： 描述 POJO对象
* @ApiModelProperty： 描述 POJO对象中的属性值
* @ApiImplicitParam： 描述单个入参信息
* @ApiImplicitParams： 描述多个入参信息
* @ApiResponse： 描述单个出参信息
* @ApiResponses： 描述多个出参信息
* @ApiError： 接口错误所返回的信息

# 访问地址
## ui地址
http://127.0.0.1:8888/swagger-ui.html

## json数据地址
http://127.0.0.1:8888/v2/api-docs

# 生产环境禁用
## 通过nginx禁用
nginx禁止访问swagger-ui.html和api-docs

## 通过springboot禁用
在swagger配置类中加`@Profile("dev")`注解
配置文件中通过spring.profiles.active类指定Profile值，让配置类只有在dev环境里才生效