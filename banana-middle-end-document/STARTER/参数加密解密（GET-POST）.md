
# 参数加密解密（GET-POST）使用指南

## Spring Boot 版本
 
 （1）	需使用Spring Boot 版本为2.0.3.RELEASE；其他版本会出现冲突及不兼容问题；

### 说明

 (1)加密/解密框架流程图;
 
    1.客户端/服务端参数加密，通过生成16位byte随机数组；
    2.客户端/服务端利用对方公钥+16位byte字节进行RSA加密；并将其进行BASE64加密；
      （1）客户端通过header进行传递；key为：client
      （2）服务端返回结果框架封装加密返回结果为两段字符串进行“&&”拼接；
            分别前半部分身份参数密钥；后半部分返回结构体参数加密串；至于身份参数密钥（下面将讲解到请求体或参数是通过生成的rsa加密KEY，进行在AES加密的）
    3.参数值/请求体加密：通过“2”后得到密钥key，在利用AES进行加密，那么密码就是KEY；
    4.参考JAVA版编码加密；解密
        
        //TODO 加密
        
        //TODO 客户端16位随机byte字节
        byte[] key = new byte[16];
        Random random = new Random();
        random.nextBytes(key);
        //TODO 公钥+16位byte字节进行RSA加密
        String enStr1 = RsaTool.encryptByPublicKey(key, publicKey); //TODO 客户端提供的公钥
        
        //TODO 参数加密身份/请求体加密身份
        //String clientBase64 = Base64Coded.encode(enStr1.getBytes());
        
        //TODO AES加密
        String bodyAES = AESUtil.encrypt("加密参数内容/加密请求体", key);
        //TODO BASE64加密
        String bodyBase64 = Base64Coded.encode(bodyAES.getBytes());
        
        
        //TODO 解密
        
        //TODO 客户端/服务端身份
        clientBase64 = Base64Coded.decode(clientBase64.getBytes());
        byte[] key = RsaTool.decryptByPrivateKeyByte(clientBase64, privateKey);//TODO 利用各自生成的私钥
        
        //TODO 请求体解密
        bodyBase64 = Base64Coded.decode(bodyBase64.getBytes());
        String body = AESUtil.decrypt(bodyBase64 , key);
        
        //TODO 以上服务端已封装完整；此部分编码片段为客户端参考；
        
        
 ![加密/解密框架流程图.jpg](https://banban-picture.oss-cn-shanghai.aliyuncs.com/aib_2019-08-27_加解密框架流程图.png)
    
（2）此组件为JAVA版服务器提供（客户端可根据自行加密与服务端进行加解密）
（2）GET/POST支持数据类型；7种（包括String）；如下：
        <table>
            <tr><td>数据类型</td></tr>
            <tr><td>byte(位)</td></tr>
            <tr><td>short(短整数)</td></tr>
            <tr><td>int(整数)</td></tr>
            <tr><td>long(长整数)</td></tr>
            <tr><td>float(单精度)</td></tr>
            <tr><td>double(双精度)</td></tr>
            <tr><td>boolean(布尔值)</td></tr>
        </table>
    
 (3) GET支持解密参数写法:
 
         public BaseResponseBody<String> getPathVariable3(@PathVariable double id, @PathVariable String str) {
                return BaseResponseBody.success("hello" + id + str);
         }
         
         public BaseResponseBody<String> getRequestParam(@RequestParam int id, @RequestParam String str) {
             return BaseResponseBody.success("hello" + id + str);
         }
 (4) POST支持解密参数写法(对象支持7种类型;包括String):
        
        public String post(@RequestBody ModelForm form) {
            return "hello" + form.getId() + form.getA();
        }
 (5) 采用注解形式进行加密/解密：
        
        @GetDeCode//(inDecode = false)//TODO GET解密
        @ReturnEnCode//(outEncode = false)//TODO 返回加密
        @GetMapping("/getPathVariable/{id}/{str}")
        public BaseResponseBody<String> getPathVariable(@PathVariable float id, @PathVariable String str) {
            return BaseResponseBody.success("hello" + id + str);
        }
 
        @PostDeCode//(inDecode = false)//TODO POST解密
        @ReturnEnCode//(outEncode = false)//TODO 返回加密
        @PostMapping("/post")
        public String post(@RequestBody ModelForm form) {
            System.out.println("aaaaaa");
            return "hello" + form.getId() + form.getA();
        }
        
 (5) 引用/使用：
        
       （1）需引入POM
             <dependency>
                 <groupId>com.banana</groupId>
                 <artifactId>banana-http-secret-spring-boot-starter</artifactId>
                 <version>${banana-spring-boot-starter.version}</version>
             </dependency>
       （2）YML需配置服务器/客户端双方公私密钥(服务器利用服务端私钥进行解密/客户端利用客户端私钥解密)：
            kitedge:
              secret:
                server:
                  privateKey: MIICdwIBADANBgkqhkiG9w0BAQEFAASCAmEwggJdAgEAAoGBAMN0ihgQAjM/FJYN07MREWjQdtzOM5SFb8QX732mTdQ1+ClxKbhk234sLP9J8KhBWRmAu/BuS3VV2l5gP3YDCDw6lIeQ9nEYsGf2HWpCTwCLW+9AUIsOWuRahWFIaF1I9i376Uc6hWmXlgoJDXfVN7By1IiOKpiS9PVi2+5NmV27AgMBAAECgYEAhO1OOZ0xkd61xnNy5ZaXC3yzSeom5Dx3zZols4OV7NwjgTPIFvLo3/91Ps3vkM6YpSKLLrIkOZGA6Kx2nHDleLaG5gHi148edvvPBMHOD/2wHcDiQP/RFHii+Y6Ccr+nJAz/zGzQG7BRu1x9bGpYr/HgsRHIUSyx/JiPbNyiRqkCQQD6MJxhb0z87nIb+iWof6ueZncpOKoJcYzLtN3Nq950nGh94RoINo1fKwodspbVS0mzGxvr0S/tCKiAd6Fj2H9/AkEAx/6HX7NhvOsf3YPYF8fR+koMJ9d55bqR4t7EqUncvkXR2hGoKKB/XbzdnfXq65iZk7IUGsqQiC0ANZOu3uE/xQJAU47oq6IAlHkOo0loSl5Zcw3Fvn6uS3khDOfbLFW6djfGrkP/7b5KeP5m4oRC2/nJI4hqStgSqZe6ry1FOZOVAQJBAJcdBE7UbKJhipdG2s3FJyyWLK8iCjICEd85caF0dP20Jcc/Fi90bih234FOYm4XxNsrcDN6IVEzp3fUGgrAaf0CQHwgCreH8GoNIHs8d5Oh2YN95nqKRaJDvR5dwc+mfjZG+SmkQ5gLryTbrvAfFgKFClgSEETjS5tmbOP2TIeHNv0=
                client:
                  publicKey: MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQCuKxx9S/eHn40dlRDXW5H5bq4LSWjjgq6XoEt9ZhKTXP3Ma8nAQwG0GvuN2mJ8sNzmiraV52ftufPsdi54fGlphRs1dWSD6R5Giiq+P+UO3O+l7AMlZlMtanH/qtoi8YVZH/3hEnDVPKPVy90kV3MgpYfvWqjpcC9ILcBVVNlaTwIDAQAB
        (3) 公私密钥生成（也可生成客户端公私密钥或客户端自行生成/也可以服务端开放API接口生成给客户端使用）：
            Map<String, Object> map = new HashMap<String, Object>();
            map = RsaTool.init();
            String publicKey = RsaTool.getPublicKey(map);
            String privateKey = RsaTool.getPrivateKey(map);
            System.out.println("服务端公钥为:" + publicKey);
            System.out.println("服务端私钥为:" + privateKey);
        （4）服务端Controller
            @GetDeCode//(inDecode = false)//TODO GET解密
                @ReturnEnCode//(outEncode = false)//TODO 返回加密
                @GetMapping("/getPathVariable5/{id}/{str}")
                public BaseResponseBody<String> getPathVariable5(@PathVariable boolean id, @PathVariable String str) {
                    return BaseResponseBody.success("hello" + id + str);
                }
            
                @GetDeCode//(inDecode = false)//TODO GET解密
                @ReturnEnCode//(outEncode = false)//TODO 返回加密
                @GetMapping("/getPathVariable6/{id}/{str}")
                public BaseResponseBody<String> getPathVariable6(@PathVariable long id, @PathVariable String str) {
                    return BaseResponseBody.success("hello" + id + str);
                }
            
                @GetDeCode//(inDecode = false)//TODO GET解密
                @ReturnEnCode//(outEncode = false)//TODO 返回加密
                @GetMapping("/getRequestParam")
                public BaseResponseBody<String> getRequestParam(@RequestParam int id, @RequestParam String str) {
                    return BaseResponseBody.success("hello" + id + str);
                }
            
                @PostDeCode//(inDecode = false)//TODO POST解密
                @ReturnEnCode//(outEncode = false)//TODO 返回加密
                @PostMapping("/post")
                public String post(@RequestBody ModelForm form) {
                    System.out.println("aaaaaa");
                    return "hello" + form.getId() + form.getA();
                }   
            
### 客户端加密/解密规则；
       如图所示；客户端需要自行实现；也可服务端暴露API接口给客户端使用；