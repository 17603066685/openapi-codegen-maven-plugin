# openapi-codegen-maven-plugin
maven插件，基于openapi2.0接口生成对应的java web框架代码

一.进行Maven包的引入 以及插件的配置

    <!-- swagger -->
    <dependency>
        <groupId>io.springfox</groupId>
        <artifactId>springfox-swagger2</artifactId>
        <exclusions>
            <exclusion>
                <groupId>io.swagger</groupId>
                <artifactId>swagger-annotations</artifactId>
            </exclusion>
            <exclusion>
                <groupId>io.swagger</groupId>
                <artifactId>swagger-models</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>io.swagger</groupId>
        <artifactId>swagger-annotations</artifactId>
        <version>1.5.21</version>
    </dependency>
    <dependency>
        <groupId>io.swagger</groupId>
        <artifactId>swagger-models</artifactId>
        <version>1.5.21</version>
    </dependency>
    <!-- swagger -->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
    </dependency>

    <dependency>
        <groupId>org.openapitools</groupId>
        <artifactId>jackson-databind-nullable</artifactId>
        <version>0.2.0</version>
    </dependency>
    <dependency>
        <groupId>org.hibernate.validator</groupId>
        <artifactId>hibernate-validator</artifactId>
    </dependency>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
        <plugin>
            <groupId>com.lindyy.codegen</groupId>
            <artifactId>openapi-codegen-maven-plugin</artifactId>
            <version>1.0.0</version>
            <executions>
                <execution>
                    <goals>
                        <goal>generate</goal>
                    </goals>
                    <configuration>
                        <inputSpec>${project.basedir}/src/main/resources/api</inputSpec>
                        <generatorName>spring</generatorName>
                        <apiPackage>com.lindyy.wcems.admin.api.rest</apiPackage>
                        <modelPackage>com.lindyy.wcems.admin.api.model</modelPackage>
                        <skipValidateSpec>false</skipValidateSpec>
                        <supportingFilesToGenerate>ApiUtil.java</supportingFilesToGenerate>
                        <configOptions>
                            <delegatePattern>true</delegatePattern>
                            <title>swagger</title>
                            <serializableModel>true</serializableModel>
                        </configOptions>
                    </configuration>
                </execution>
            </executions>
        </plugin>
    </plugins>

二.编写yaml api接口（基于openAPI）

```
swagger: "2.0"
info:
  title: '用户管理API文档'
  description: '用户管理API文档'
  version: '0.1'
schemes:
  - "http"
tags:
  - name: User
    description: 用户管理
paths:
  /user/save:
    post:
      tags:
        - "User"
      description: "保存用户"
      operationId: saveUser
      produces:
        - "application/json"
      parameters:
        - in: body
          name: "user"
          required: true
          schema:
            $ref: "#/definitions/UserDto"
      responses:
        200:
          description: "返回结果"
          schema:
            type: object
            $ref: "#/definitions/Response"
definitions:
  Response:
    type: object
    properties:
      success:
        type: boolean
      code:
        type: integer
      message:
        type: string
      data:
        type: object

  ExtendsExmple:
    allOf:
      - $ref: "#/definitions/Response"
      - type: object
  UserDto:
    type: object
    required:
      - userNumber
      - userName
    properties:
      id:
        type: integer
        format: int64
      userNumber:
        type: string
      userName:
        type: string
      phone:
        type: string
      userStatus:
        type: string
      operatorNumber:
        type: string
```

三.打包编译

`mvn clean install`