# OSS使用指南
### 配置
  (1)pom引入
  
      <dependency>
        <groupId>com.banana</groupId>
        <artifactId>banana-oss-spring-boot-starter</artifactId>
        <version>${banana-spring-boot-starter.version}</version>
      </dependency>
   
### 使用

    import com.banana.oss.config.OSSFactory;

    @Api(tags = "oss")
    @RestController
    @Slf4j
    public class OSSController {
        @ApiOperation(value = "上传")
        @PostMapping("/oss/uploadFile")
        public BaseResponseBody<String> redis(@RequestParam("file") MultipartFile file) {
            String url = "";
            try {
                url = OSSFactory.build().uploadFileSuffix(file.getBytes(), "",
                        String.valueOf(System.currentTimeMillis())+"/" + file.getOriginalFilename());
            } catch (IOException e) {
                e.printStackTrace();
            }
            return BaseResponseBody.from(MyStatusCode.test, url);
        }
    }