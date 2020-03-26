# RabbitMQ使用指南
### 配置
  (1) yml配置文件
    
    rabbitmq:
      addresses: 192.168.31.15:5672 #集群多个逗号','分隔
      username: mqadmin # 账号
      password: Nwvh3#vu@kqLP&FdHt # 密码
      exchange: banan_demo.exchange_direct_demo # 交换机
      routingKey: banan_demo.routing_direct_demo # 路由密钥
      custom-confirm-callback: false #是否自定义确认
      custom-return-callback: false #是否自定义返回异常
      exchange-type: direct # direct,fanout,topic 交换机/广播/主题
      queues: # 多个定义主题
        -banan_demo.test1
        -banan_demo.test2
        -banan_demo.test3
      #    virtual-host: #虚拟主机名
      connection-timeout: 5000 #连接超时
      template:
        mandatory: true #模板
      listener: #系统listener，可自定义抽象，后期完成
        simple:
          concurrency: 1 #并发数
          max-concurrency: 5 #最大并发数
          prefetch: 1 #限流, 避免同时处理大量消息导致服务器 down 机, 根据线程数来决定
          
  (2) pom引入
   
    <dependency>
        <groupId>com.banana</groupId>
        <artifactId>banana-amqp-spring-boot-starter</artifactId>
        <version>${banana-spring-boot-starter.version}</version>
    </dependency>
    
### 使用
  
  (1)生产者
        
       import com.banana.starter.mq.service.IMQTool;
        
       //TODO 先注入mq组件
       @Resource
       private IMQTool mqTool;
       //TODO 两种发送消息的方式
       //TODO boolean sendRabbitMsg(String msg);
       //TODO boolean sendRabbitMsg(String msg,
       								 RabbitTemplate.ConfirmCallback confirmCallback,
       								 RabbitTemplate.ReturnCallback returnCallback);
       
       //第一种，发送消息
       public void mq() {
            mqTool.sendRabbitMsg("办伴科技");
       }
       
       //TODO 第二种，发送消息,自定义确认以及自定义返回
       //TODO 配置文件的custom-confirm-callback和custom-return-callback要设置为true
       public void confirmCallBackMq() {
           mqTool.sendRabbitMsg("办伴科技", new RabbitTemplate.ConfirmCallback() {
                       @Override
                       public void confirm(CorrelationData correlationData, boolean ack, String cause) {
                           log.info("===correlationData_id: " + correlationData.getId() + "===ack:" + ack);
                           if(!ack){
                               log.error("消息异常.");
                           }
                       }
                   },
                   new RabbitTemplate.ReturnCallback() {
                       @Override
                       public void returnedMessage(Message message, int replyCode, String replyText, String exchange, String routingKey) {
                           log.info("消息丢失:exchange({}),route({}),replyCode({}),replyText({}),message:{}",
                                   exchange, routingKey, replyCode,replyText, message);
                       }
                   }
               );
       }
       
  (2)消费者
    
    package com.test.listener;
    
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.amqp.rabbit.annotation.RabbitListener;
    import org.springframework.stereotype.Service;
    
    @Service
    @Slf4j
    public class TestListener {
        
        //TODO 队列名称为yml配置文件里面的queues列表项
        @RabbitListener(queues = "banan_demo.test2")
        public void handleMessage(String text) {
            System.out.println("Received--------------------------: " + text);
        }
    }
