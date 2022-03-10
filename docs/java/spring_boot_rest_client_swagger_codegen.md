# Generate Spring Boot REST Client with Swagger
Last modified: March 23, 2020

by baeldungRESTSpring BootSwagger
Get started with Spring 5 and Spring Boot 2, through the reference Learn Spring course:

> CHECK OUT THE COURSE
 
1. Introduction
   In this article, we'll use the Swagger Codegen and OpenAPI Generator projects to generate REST clients from an OpenAPI/Swagger spec file.

Also, we'll create a Spring Boot project, where we'll use generated classes.

We'll use the Swagger Petstore API example for everything.

2. Generate REST Client With Swagger Codegen
   Swagger provides a utility jar that allows us to generate REST clients for various programming languages and multiple frameworks.

2.1. Download Jar File
The code-gen_cli.jar can be downloaded from here.

For the newest version, please check the swagger-codegen-cli repository.

2.2. Generate Client
Let's generate our client by executing the command java -jar swagger-code-gen-cli.jar generate:
```shell
   java -jar swagger-codegen-cli.jar generate \
   -i http://petstore.swagger.io/v2/swagger.json \
   --api-package com.baeldung.petstore.client.api \
   --model-package com.baeldung.petstore.client.model \
   --invoker-package com.baeldung.petstore.client.invoker \
   --group-id com.baeldung \
   --artifact-id spring-swagger-codegen-api-client \
   --artifact-version 0.0.1-SNAPSHOT \
   -l java \
   --library resttemplate \
   -o spring-swagger-codegen-api-client
```

The provided arguments consist of:

A source swagger file URL or path – provided using the -i argument
Names of packages for generated classes – provided using –api-package, –model-package, –invoker-package
Generated Maven project properties –group-id, –artifact-id, –artifact-version
The programming language of the generated client – provided using -l
The implementation framework – provided using the –library
The output directory – provided using -o
To list all Java-related options, type the following command:

```shell
  java -jar swagger-codegen-cli.jar config-help -l java
```
Swagger Codegen supports the following Java libraries (pairs of HTTP clients and JSON processing libraries):

- jersey1 – Jersey1 + Jackson
- jersey2 – Jersey2 + Jackson
- feign – OpenFeign + Jackson
- okhttp-gson – OkHttp + Gson
- retrofit (Obsolete) – Retrofit1/OkHttp + Gson
- retrofit2 – Retrofit2/OkHttp + Gson
- rest-template – Spring RestTemplate + Jackson
- rest-easy – Resteasy + Jackson

In this write-up, we chose rest-template as it's a part of the Spring ecosystem.

## 3. Generate REST Client With OpenAPI Generator
   OpenAPI Generator is a fork of Swagger Codegen capable of generating 50+ clients from any OpenAPI Specification 2.0/3.x documents.

Whereas Swagger Codegen is maintained by SmartBear, OpenAPI Generator is maintained by a community that includes more than 40 of the top contributors and template creators of Swagger Codegen as founding team members.

3.1. Installation
Perhaps the easiest and most portable installation method is using the npm package wrapper, which works by providing a CLI wrapper atop the command-line options supported by the Java code. Installation is straightforward:

npm install @openapitools/openapi-generator-cli -g
For those wanting the JAR file, it can be found in Maven Central. Let's download it now:

wget https://repo1.maven.org/maven2/org/openapitools/openapi-generator-cli/4.2.3/openapi-generator-cli-4.2.3.jar \
-O openapi-generator-cli.jar

3.2. Generate Client
First, the options for OpenAPI Generator are almost identical to those for Swagger Codegen. The most notable difference is the replacement of the -l language flag with the -g generator flag, which takes the language to generate the client as a parameter.

Next, let's generate a client equivalent to the one we generated with Swagger Codegen using the jar command:
```shell
   java -jar openapi-generator-cli.jar generate \
   -i http://petstore.swagger.io/v2/swagger.json \
   --api-package com.baeldung.petstore.client.api \
   --model-package com.baeldung.petstore.client.model \
   --invoker-package com.baeldung.petstore.client.invoker \
   --group-id com.baeldung \
   --artifact-id spring-openapi-generator-api-client \
   --artifact-version 0.0.1-SNAPSHOT \
   -g java \
   -p java8=true \
   --library resttemplate \
   -o spring-openapi-generator-api-client
```

To list all Java-related options, type the command:

java -jar openapi-generator-cli.jar config-help -g java
OpenAPI Generator supports all of the same Java libraries as Swagger CodeGen plus a few extra. The following Java libraries (pairs of HTTP clients and JSON processing libraries) are supported by OpenAPI Generator:

jersey1 – Jersey1 + Jackson
jersey2 – Jersey2 + Jackson
feign – OpenFeign + Jackson
okhttp-gson – OkHttp + Gson
retrofit (Obsolete) – Retrofit1/OkHttp + Gson
retrofit2 – Retrofit2/OkHttp + Gson
resttemplate – Spring RestTemplate + Jackson
webclient – Spring 5 WebClient + Jackson (OpenAPI Generator only)
resteasy – Resteasy + Jackson
vertx – VertX + Jackson
google-api-client – Google API Client + Jackson
rest-assured – rest-assured + Jackson/Gson (Java 8 only)
native – Java native HttpClient + Jackson (Java 11 only; OpenAPI Generator only)
microprofile – Microprofile client + Jackson (OpenAPI Generator only)
## 4. Generate Spring Boot Project
   Let's now create a new Spring Boot project.

4.1. Maven Dependency
We'll first add the dependency of the Generated API Client library – to our project pom.xml file:

```xml
<dependency>
    <groupId>com.baeldung</groupId>
    <artifactId>spring-swagger-codegen-api-client</artifactId>
    <version>0.0.1-SNAPSHOT</version>
</dependency>
```
4.2. Expose API Classes as Spring Beans
To access the generated classes, we need to configure them as beans:
```java

@Configuration
public class PetStoreIntegrationConfig {

    @Bean
    public PetApi petApi() {
        return new PetApi(apiClient());
    }
    
    @Bean
    public ApiClient apiClient() {
        return new ApiClient();
    }
}
```

4.3. API Client Configuration
The ApiClient class is used for configuring authentication, the base path of the API, common headers, and it's responsible for executing all API requests.

For example, if you're working with OAuth:

```java
@Configuration
public class PetStoreIntegrationConfig {
   @Bean
   public ApiClient apiClient() {
      ApiClient apiClient = new ApiClient();

      OAuth petStoreAuth = (OAuth) apiClient.getAuthentication("petstore_auth");
      petStoreAuth.setAccessToken("special-key");

      return apiClient;
   }
}
```
4.4. Spring Main Application
We need to import the newly created configuration:
```java
   @SpringBootApplication
   @Import(PetStoreIntegrationConfig.class)
   public class PetStoreApplication {
      public static void main(String[] args) throws Exception {
      SpringApplication.run(PetStoreApplication.class, args);
      }
   }
```
4.5. API Usage
Since we configured our API classes as beans, we can freely inject them in our Spring-managed classes:

@Autowired
private PetApi petApi;

public List<Pet> findAvailablePets() {
return petApi.findPetsByStatus(Arrays.asList("available"));
}
## 5. Alternative Solutions
   There are other ways of generating a REST client other than executing Swagger Codegen or OpenAPI Generator CLI.

5.1. Maven Plugin
A swagger-codegen Maven plugin that can be configured easily in your pom.xml allows generating the client with the same options as Swagger Codegen CLI.

This is a basic code snippet that we can include in our project's pom.xml to generate client automatically:
```xml
<plugin>
    <groupId>io.swagger</groupId>
    <artifactId>swagger-codegen-maven-plugin</artifactId>
    <version>2.2.3</version>
    <executions>
        <execution>
            <goals>
                <goal>generate</goal>
            </goals>
            <configuration>
                <inputSpec>swagger.yaml</inputSpec>
                <language>java</language>
                <library>resttemplate</library>
            </configuration>
        </execution>
    </executions>
</plugin>
```

5.2. Swagger Codegen Online Generator API
An already published API that helps us with generating the client by sending a POST request to the URL http://generator.swagger.io/api/gen/clients/java passing the spec URL alongside with other options in the request body.

Let's do an example using a simple curl command:
```shell
   curl -X POST -H "content-type:application/json" \
      -d '{"swaggerUrl":"http://petstore.swagger.io/v2/swagger.json"}' \
      http://generator.swagger.io/api/gen/clients/java
```

The response would be JSON format that contains a downloadable link that contains the generated client code in zip format. You may pass the same options used in the Swaager Codegen CLI to customize the output client.

https://generator.swagger.io contains a Swagger documentation for the API where we can check its documentation and try it.

5.3. OpenAPI Generator Online Generator API
Like Swagger Godegen, OpenAPI Generator also has an online generator. Let's perform an example using a simple curl command:
```shell
   curl -X POST -H "content-type:application/json" \
      -d '{"openAPIUrl":"http://petstore.swagger.io/v2/swagger.json"}' \
      http://api.openapi-generator.tech/api/gen/clients/java
```
The response, in JSON format, will contain a downloadable link to the generated client code in zip format. You may pass the same options used in the Swagger Codegen CLI to customize the output client.

https://github.com/OpenAPITools/openapi-generator/blob/master/docs/online.md contains the documentation for the API.

## 6. Conclusion
   Swagger Codegen and OpenAPI Generator enable you to generate REST clients quickly for your API with many languages and with the library of your choice. We can generate the client library using a CLI tool, Maven plugin, or Online API.

This is a Maven-based project that contains three Maven modules: the generated Swagger API client, the generated OpenAPI client, and the Spring Boot application.

As always, you can find the code available over on GitHub.