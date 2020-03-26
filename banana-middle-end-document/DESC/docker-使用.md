# DOCKER使用

### 介绍
    以下配置支持本地打包镜像插件，后期与运维对接，有可能改动部分dockerfile文件内容
        
#### 设置docker官方插件-目前最新1.2.0；
    <!--本地dockerfile构建镜像-->
    <plugin>
        <groupId>com.spotify</groupId>
        <artifactId>docker-maven-plugin</artifactId>
        <version>1.0.0</version>
        <!--将插件绑定在某个phase执行；mvn package ，
        就会自动执行mvn docker:build，可以将此配置移除-->
        <executions>
            <execution>
                <id>build-image</id>
                <phase>package</phase>
                <goals>
                    <goal>build</goal>
                </goals>
            </execution>
        </executions>

        <configuration>
            <!-- 是否跳过此工程镜像打包，可通过properties配置，mvn -Dxxx=?形式自定义>
            <!-- <skipDocker>${admin.docker.skip}</skipDocker> -->
            
            <!-- 镜像名称 ?/ 前缀，最好有前缀，当然最好公司名称开头 -->
            <imageName>kitedge/${project.artifactId}</imageName>
            <imageTags>
                <!-- 镜像版本，跟着自己工程版本走 -->
                <imageTag>${project.version}</imageTag>
            </imageTags>
            <!-- Dockerfile文件路径，放在项目工程根本路即可,或者也可以自定放在任何工程resources下，
             指定${project.basedir}/src/main/resources/?/? -->
            <dockerDirectory>${project.basedir}</dockerDirectory>
            
            <!-- 本地打包镜像 -->
            <!-- <dockerHost>http://10.203.202.80:2375</dockerHost> -->
            <resources>
                <resource>
                    <targetPath>/</targetPath>
                    <!-- 文件包路径，${project.build.finalName}项目tarage/打包名称 -->
                    <directory>${project.build.directory}</directory>
                    <include>${project.build.finalName}.jar</include>
                </resource>
            </resources>
        </configuration>
    </plugin>
             
#### Dockerfile（建立Dockerfile内容如下）：
     ##项目所依赖的镜像JDK
     FROM java:8
     VOLUME /tmptest
     ADD target/*.jar /jar/app.jar
     
     ##项目所依赖端口可以多个，根据几种环境多添加
     EXPOSE 8769
     EXPOSE 18769
     EXPOSE 28769
     
     ##容器系统对应时间24小时制
     RUN cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
     && echo 'Asia/Shanghai' >/etc/timezone
     
     ##启动容器对应变量，例 docker run -d -e PARAMS="-Dx=x -Dx=x"
     ENV PARAMS=""
     
     ##支持启动容器对应变量，SH模式
     ENTRYPOINT ["sh","-c","java $PARAMS -jar /jar/app.jar"]