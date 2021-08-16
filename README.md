
# openapi-codegen-maven-plugin
maven插件，基于openapi-generator-maven-plugin 4.0.3 开源项目 基于openAPI接口规范生成对应的java web框架代码，原本的插件只支持读取一个yaml文件，现在将他改成读取一个目录下的所有yaml文件，
然后打包编译自动生成代码，保证每个controller一个yaml文件，易于管理

使用
============================

* 进行Maven包的引入 以及插件的配置


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


* 编写yaml api接口（基于openAPI）

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

* 打包编译

`mvn clean install`


### 一般参数配置


具体可以看 https://github.com/OpenAPITools/openapi-generator/tree/master/modules/openapi-generator-maven-plugin


| Option | Property | Description |
|--------|----------|-------------|
| `inputSpec` |  `openapi.generator.maven.plugin.inputSpec` | OpenAPI Spec file path
| `language` |  `openapi.generator.maven.plugin.language` | target generation language (deprecated, replaced by `generatorName` as values here don't represent only 'language' any longer)
| `generatorName` |  `openapi.generator.maven.plugin.generatorName` | target generator name
| `output` |  `openapi.generator.maven.plugin.output` | target output path (default is `${project.build.directory}/generated-sources/openapi`. Can also be set globally through the `openapi.generator.maven.plugin.output` property)
| `templateDirectory` |  `openapi.generator.maven.plugin.templateDirectory` | directory with mustache templates
| `addCompileSourceRoot` |  `openapi.generator.maven.plugin.addCompileSourceRoot` | add the output directory to the project as a source root (`true` by default)
| `modelPackage` |  `openapi.generator.maven.plugin.modelPackage` | the package to use for generated model objects/classes
| `apiPackage` |  `openapi.generator.maven.plugin.apiPackage` | the package to use for generated api objects/classes
| `invokerPackage` |  `openapi.generator.maven.plugin.invokerPackage` | the package to use for the generated invoker objects
| `modelNamePrefix` |  `openapi.generator.maven.plugin.modelNamePrefix` | Sets the prefix for model classes and enums
| `modelNameSuffix` |  `openapi.generator.maven.plugin.modelNameSuffix` | Sets the suffix for model classes and enums
| `withXml` |  `openapi.generator.maven.plugin.withXml` | enable XML annotations inside the generated models and API (only works with Java `language` and libraries that provide support for JSON and XML)
| `configOptions` |  N/A | a map of language-specific parameters. To show a full list of generator-specified parameters (options), please use `configHelp` (explained below)
| `configHelp` |  `codegen.configHelp` | dumps the configuration help for the specified library (generates no sources)
| `ignoreFileOverride` |  `openapi.generator.maven.plugin.ignoreFileOverride` | specifies the full path to a `.openapi-generator-ignore` used for pattern based overrides of generated outputs
| `removeOperationIdPrefix` |  `openapi.generator.maven.plugin.removeOperationIdPrefix` | remove operationId prefix (e.g. user_getName => getName)
| `logToStderr` |  `openapi.generator.maven.plugin.logToStderr` | write all log messages (not just errors) to STDOUT
| `enablePostProcessFile` |  `openapi.generator.maven.plugin.` | enable file post-processing hook
| `skipValidateSpec` |  `openapi.generator.maven.plugin.skipValidateSpec` | Whether or not to skip validating the input spec prior to generation. By default, invalid specifications will result in an error.
| `strictSpec` |  `openapi.generator.maven.plugin.strictSpec` | Whether or not to treat an input document strictly against the spec. 'MUST' and 'SHALL' wording in OpenAPI spec is strictly adhered to. e.g. when false, no fixes will be applied to documents which pass validation but don't follow the spec.
| `generateAliasAsModel` |  `openapi.generator.maven.plugin.generateAliasAsModel` | generate alias (array, map) as model
| `generateApis` |  `openapi.generator.maven.plugin.generateApis` | generate the apis (`true` by default)
| `generateApiTests` |  `openapi.generator.maven.plugin.generateApiTests` | generate the api tests (`true` by default. Only available if `generateApis` is `true`)
| `generateApiDocumentation` |  `openapi.generator.maven.plugin.generateApiDocumentation` | generate the api documentation (`true` by default. Only available if `generateApis` is `true`)
| `generateModels` |  `openapi.generator.maven.plugin.generateModels` | generate the models (`true` by default)
| `modelsToGenerate` |  `openapi.generator.maven.plugin.modelsToGenerate` | A comma separated list of models to generate.  All models is the default.
| `generateModelTests` |  `openapi.generator.maven.plugin.generateModelTests` | generate the model tests (`true` by default. Only available if `generateModels` is `true`)
| `generateModelDocumentation` |  `openapi.generator.maven.plugin.generateModelDocumentation` | generate the model documentation (`true` by default. Only available if `generateModels` is `true`)
| `generateSupportingFiles` |  `openapi.generator.maven.plugin.generateSupportingFiles` | generate the supporting files (`true` by default)
| `supportingFilesToGenerate` |  `openapi.generator.maven.plugin.supportingFilesToGenerate` | A comma separated list of supporting files to generate.  All files is the default.
| `skip` |  `codegen.skip` | skip code generation (`false` by default. Can also be set globally through the `codegen.skip` property)
| `verbose` |  `openapi.generator.maven.plugin.verbose` | verbose mode (`false` by default)
| `groupId` | `openapi.generator.maven.plugin.groupId`  | sets project information in generated pom.xml/build.gradle or other build script. Language-specific conversions occur in non-jvm generators
| `artifactId` |  `openapi.generator.maven.plugin.artifactId` | sets project information in generated pom.xml/build.gradle or other build script. Language-specific conversions occur in non-jvm generators
| `artifactVersion`  |  `openapi.generator.maven.plugin.artifactVersion` | sets project information in generated pom.xml/build.gradle or other build script. Language-specific conversions occur in non-jvm generators
| `gitUserId` and `gitRepoId` |  `openapi.generator.maven.plugin.gitUserId` | sets git information of the project
| `auth` |  `openapi.generator.maven.plugin.auth` | adds authorization headers when fetching the OpenAPI definitions remotely. Pass in a URL-encoded string of `name:header` with a comma separating multiple values
| `configurationFile` |  `openapi.generator.maven.plugin.configurationFile` | Path to separate json configuration file. File content should be in a json format {"optionKey":"optionValue", "optionKey1":"optionValue1"...} Supported options can be different for each language. Run `config-help -g {generator name}` command for language specific config options
| `skipOverwrite` |  `openapi.generator.maven.plugin.skipOverwrite` | Specifies if the existing files should be overwritten during the generation. (`false` by default)
| `library` |  `openapi.generator.maven.plugin.library` | library template (sub-template)
| `instantiationTypes` |  `openapi.generator.maven.plugin.instantiationTypes` | sets instantiation type mappings in the format of type=instantiatedType,type=instantiatedType. For example (in Java): `array=ArrayList,map=HashMap`. In other words array types will get instantiated as ArrayList in generated code. You can also have multiple occurrences of this option
| `importMappings` |  `openapi.generator.maven.plugin.importMappings` | specifies mappings between a given class and the import that should be used for that class in the format of type=import,type=import. You can also have multiple occurrences of this option
| `typeMappings` |  `openapi.generator.maven.plugin.typeMappings` | sets mappings between OpenAPI spec types and generated code types in the format of OpenAPIType=generatedType,OpenAPIType=generatedType. For example: `array=List,map=Map,string=String`. You can also have multiple occurrences of this option
| `languageSpecificPrimitives` |  `openapi.generator.maven.plugin.languageSpecificPrimitives` | specifies additional language specific primitive types in the format of type1,type2,type3,type3. For example: `String,boolean,Boolean,Double`. You can also have multiple occurrences of this option
| `additionalProperties` |  `openapi.generator.maven.plugin.additionalProperties` | sets additional properties that can be referenced by the mustache templates in the format of name=value,name=value. You can also have multiple occurrences of this option
| `reservedWordsMappings` |  `openapi.generator.maven.plugin.reservedWordsMappings` | specifies how a reserved name should be escaped to. Otherwise, the default `_<name>` is used. For example `id=identifier`. You can also have multiple occurrences of this option
| `skipIfSpecIsUnchanged` |  `codegen.skipIfSpecIsUnchanged` | Skip the execution if the source file is older than the output folder (`false` by default. Can also be set globally through the `codegen.skipIfSpecIsUnchanged` property)
| `engine` | `openapi.generator.maven.plugin.engine` | The name of templating engine to use, "mustache" (default) or "handlebars" (beta)

