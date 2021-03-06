# microservices-eureka

This is the demonstration of microservice architecture using Eureka Server from Spring Cloud NetFlix. Eureka as service discovery server enables microservices to locate the different instances of another services without knowing their addresses(hostname, ip or url) for purposes of loadbalancing and failover. 

Apart from Eureka, Ribbon and Feign from Netflix Spring Cloud were also used in our demonstration. The primary task of Netflix Ribbon is to provide client-side load balancing.Ribbon load balancers provide service discovery in dynamic environments like a cloud. Ribbon API can dynamically determine whether the servers are up and running in a live environment and can detect those servers that are down. 

Netflix Feign is a declarative HTTP client which aims at simplifying HTTP API clients. By means of  Feign, the developer needs only to declare and annotate an interface while the actual HTTP request implementation will happen at runtime.

# Starting up SpringBoot application as Eureka Server
`@EnableEurekaServer` annotation should be added next to `@SpringBootApplication` annotation on the Main class the SpringBoot application which owns main() function. Maven file for reference can be found in the repository in order to build and run Eureka Server. I used version Greenwich.SR3 for the spring-cloud-starter-parent which loads spring-boot-starter-parent 2.1.7

```xml
    <parent>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-parent</artifactId>
        <version>Greenwich.SR3</version>
        <relativePath/>
    </parent>
    
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
    </dependencies>
```
It is the off the shelf module provided by Netflix and fairly simply to make it running. 

`application.yml` for Eureka Server :

```yml
spring:
  application:
    name: eureka-server
server:
  port: ${vcap.application.port:8761}

eureka:
  client:
    registerWithEureka: false #
    fetchRegistry: false
  server:
    waitTimeInMsWhenSyncEmpty: 0
  instance:
    prefer-ip-address: true

logging:
  level:
    root: INFO
    org.springframework: INFO


```

### Notes
 
If `registerWithEureka` and `fetchRegistry` are set to true, while starting the Eureka server, the inbuilt client tries to register itself with the Eureka server and it also tries to fetch registry which is not yet available. Those properties should be set to false.

When Eureka first starts up without peer Eureka servers, it waits for all clients to register completely before it can give out the list. In order to prevent delays on production environment `waitTimeInMsWhenSyncEmpty` could be set to 0

`prefer-ip-address= true` forces the clients to register with an ip address instead of a host name

# Starting up SpringBoot application as Eureka Client

@EnableEurekaClient annotation should be added next to @SpringBootApplication annotation on the Main class the SpringBoot application which owns main() function. Maven file for reference can be found in the repository in order to build and run Eureka Server. I used version Greenwich.SR3 for the spring-cloud-starter-parent which loads spring-boot-starter-parent 2.1.7

```xml
    <parent>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-parent</artifactId>
        <version>Greenwich.SR3</version>
        <relativePath/>
    </parent>
    
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

`application.yml` for Eureka client 

```yml

server:
  port: ${vcap.application.port:9999}

spring:
  application:
    name: alert-service
  datasource:
    url: jdbc:mysql://127.0.0.1:3306/alert_engine?autoReconnect=true&zeroDateTimeBehavior=convertToNull
    driver-class-name: com.mysql.cj.jdbc.Driver
    username: root
    password: root12
  jpa:
    generate-ddl: true
    hibernate:
      ddl-auto: update
  jackson:
    date-format: yyyy-MM-dd HH:mm:ss
  mail:
    host: mail.voidaint.com
    port: 587
    username: pars@voidaint.com
    password: Pars123321
    properties:
      mail:
        smtp:
          auth: true
          starttls:
            enable: false

# Service configs
alert:
  process:
    period:
      seconds: 20

mail:
  from: notify@voidaint.com
---
eureka:
  instance:
    appname: ALERT-SERVICE
  client:
    serviceUrl:
      defaultZone: ${vcap.services.eureka-service.credentials.uri:http://127.0.0.1:8761}/eureka/

```

### Notes

`defaultZone` is the fallback value that provides the service URL for any client that does not express a preference

