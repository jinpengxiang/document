# Swagger使用指南
### 配置
     
  (1) pom引入
   
        <dependency>
            <groupId>com.banana</groupId>
            <artifactId>banana-swagger-spring-boot-starter</artifactId>
            <version>${banana-spring-boot-starter.version}</version>
        </dependency>
        
  ### 使用
     
     //控制器@Api注解
     @Api(tags = "MQ")
     @RestController
     public class MQController{}
     
     
     
     /**方法层面上注解
      * @ApiOperation 指定接口方法的说明，如果方法不加此注解，说明默认为方法名称
      * @ApiImplicitParams @ApiImplicitParam 接口详细描述的参数信息
      * paramType代表参数放在哪个地方
      *   header-->请求参数的获取：@RequestHeader
      *   query-->请求参数的获取：@RequestParam
      *   path（用于restful接口）-->请求参数的获取：@PathVariable
      *   body（不常用）
      *   form（不常用）
      */
     @ApiOperation(value = "根据id查询商品详情", notes = "查询某个商品的详细信息")
     @ApiImplicitParam(name = "id", value = "商品id", paramType = "path", required = true, dataType ="String")
     @ApiImplicitParams({
             @ApiImplicitParam(name = "id", value = "用户id", dataType = "String", paramType = "path", example = "1112")
     })
     @ApiResponses({
             @ApiResponse(code = 500, message = "服务器内部错误"),
             @ApiResponse(code = 404, message = "找不到请求路径")
     })
     @RequestMapping(value = "getGoods/{id}", method = RequestMethod.GET)
     public String GetGoods(@PathVariable String id) {
         return "Leon-商品ID : " + id;
     }
   