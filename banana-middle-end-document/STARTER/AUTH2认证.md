#AUTH2认证
###配置
   (1) yml配置文件
   
      banana:  
        auth:
          accessTokenValiditySeconds: 180000 # ACCESS_TOKEN有效时间;单位:秒(默认50小时)
          refreshTokenValiditySeconds: 280000 # REFRESH_TOKEN效时间;单位:秒(默认77小时+)
          clientId: banana # oauth_client_details 权限接入账号
          clientSecret: hOIXOgZNITg1nh2Wp2Qr # oauth_client_details权限接入密钥
          redisPrefix: demo # redis前缀
        
       
   (2) pom引入
   
       <dependency>
         <groupId>com.banana</groupId>
         <artifactId>banana-auth2-spring-boot-starter</artifactId>
         <version>${banana-spring-boot-starter.version}</version>
       </dependency>
     
### 使用
  