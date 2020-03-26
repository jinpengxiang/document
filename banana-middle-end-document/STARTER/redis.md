# Redis使用指南
### 配置
  (1)yml配置
    
    redis: #redis 兼容单节点；集群；分布式；支持8种数据类型+自定义对象
        redisPrefix: test
        activeProfile: ${spring.profiles}
        expireSecond: 30
        cache:
    #      nodes:
    #        -192.168.159.129:7001
    #        -192.168.159.129:7002
    #        -192.168.159.129:7003
    #        -192.168.159.129:7004
    #        -192.168.159.129:7005
    #        -192.168.159.129:7006
          database: 10 #Redis数据库索引
          host: 192.168.31.15:6379
          maxIdle: 10 # 最大空闲连接
          minIdle: 5 # 最小空闲连接
          maxTotal: 20 # 最大连接数
          maxWaitMillis: 5000 # 最大等待毫秒数
          password: BM6^#waC7s$32
          
  (2)pom引入
  
    <dependency>
        <groupId>com.banana</groupId>
        <artifactId>banana-redis-spring-boot-starter</artifactId>
        <version>${banana-spring-boot-starter.version}</version>
    </dependency>
  
### 使用
  
      import com.banana.redis.config.IRedisTool;
  
      //TODO 组件引入
      @Resource
      private IRedisTool redisTool;
      
      //TODO 存入字符串
      Admin admin = new Admin();
      admin.setAge(11);
      admin.setUsername("李白");
      //TODO 第三个参数为缓存过期时间，单位秒
      redisTool.set("admin",admin);
      redisTool.set("name","韩信");
      //TODO 取出字符串
      Admin admin2 = redisTool.get("admin",Admin.class);
      String username = redisTool.get("admin",String.class);
      //TODO 删除字符串
      redisTool.delete("admin");
      redisTool.delete("name");
      
      //TODO 存入list
      List  collection = new ArrayList<>();
      Admin admin = new Admin();
      admin.setAge(14);
      admin.setUsername("小明");
      Admin admin2 = new Admin();
      admin2.setAge(13);
      admin2.setUsername("鲁班");
      collection.add(admin);
      collection.add(admin2);
      //TODO 第三个参数为缓存过期时间，单位秒
      redisTool.listSet("test_list_admin", collection);
      //TODO 取出list
      ArrayList<Admin> list = (ArrayList<Admin>)redisTool.listGet("test_list_admin", Admin.class);
      for(Admin s : list){
          System.out.println(s.getUsername());
      }
      
      //TODO 存入hash
      Admin admin = new Admin();
      admin.setAge(15);
      admin.setUsername("张飞");
      //TODO 第四个参数为缓存过期时间，单位秒
      redisTool.hset("test_has", "has_en", admin);
      //TODO 取出hash
      Admin dd = redisTool.hget("test_has", "has_en", Admin.class);
      //TODO 删除hash
      redisTool.delete("test_has","has_en");
      
      //TODO 判断缓存是否存在
      Boolean flag = redisTool.exits("test_has");