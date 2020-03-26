# 执行器-API使用指南
### POM引用:(http://nexus3.hanyuan.vip:9081/repository/maven-public下载)
    <dependency>
      <groupId>com.banana</groupId>
      <artifactId>banana-middle-end-job-api</artifactId>
      <version>1.0.5.RELEASE</version>
    </dependency>
    
 (3) jackson日期转换配置
 
        spring:
          jackson:
            date-format: yyyy-MM-dd HH:mm:ss
            time-zone: GMT+8
            
 (4) 启动类扫描包配置
        
        @EnableFeignClients("com.banana")
        @EnableDiscoveryClient
        @SpringBootApplication
        public class AppTestAction {
            public static void main(String[] args) {
                SpringApplication.run(AppTestAction.class, args);
            }
        }