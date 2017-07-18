# Service Registry and Discovery

## Eureka server

It is really easy to use Eureka with Spring Boot. The first thing you need to start is obviously the server:

1. Create a spring boot application and add Eureka Server to the list of dependencies.
2. Open EurekaServerApplication.java (class containing main method) and add @EnableEurekaServer. It should look like this:
```
@SpringBootApplication
@EnableEurekaServer
public class EurekaServerApplication {

	public static void main(String[] args) {
		SpringApplication.run(EurekaServerApplication.class, args);
	}
}
```
3. Add the following lines to application.yml to configure the server: (I prefer YAML, but it is also possible to use application.properties which is provided by the project from the Spring Initializer)
```
server:
  port: 8761

spring:
  application:
    name: eureka-server

eureka:
  client:
    register-with-eureka: false
    fetch-registry: false
```

The properties registerWithEureka: false and fetchRegistry: false prevent that the application acts as a Eureka client and server at the same time.

## Eureka client
The next step is to create an application that registers with the service registry. 
1. Create another spring boot project and this time add Eureka Discovery as a dependency:
2. Add @EnableEurekaClient or @EnableDiscoveryClient (I prefer @EnableDiscoveryClient) in the Main class:
```
@SpringBootApplication
@EnableDiscoveryClient
public class ProfileServiceApplication {

	public static void main(String[] args) {
		SpringApplication.run(ProfileServiceApplication.class, args);
	}
}
```

> There are multiple implementations of "Discovery Service" (eureka, consul, zookeeper). @EnableDiscoveryClient lives in spring-cloud-commons and picks the implementation on the classpath.  @EnableEurekaClient lives in spring-cloud-netflix and only works for eureka. If eureka is on your classpath, they are effectively the same.

3. In application.yml only the application name and the default zone need to be set. The name will be used to register and the defaultZone is the URL of the Eureka server. For a local setup this will be http://127.0.0.1:8761/eureka/.
```
server:
  port: 7000
  
spring:
  application:
    name: test-service
    
eureka:
  client:
    service-url:
      defaultZone: http://127.0.0.1:8761/eureka/
```

If you start both applications, the logs show that the testApp registers its instance:

Log output of Eureka-Client:

> DiscoveryClient_TEST-SERVICE/<Full Computer Name>:test-service:7000: registering service...

> DiscoveryClient_TEST-SERVICE/<Full Computer Name>:test-service:7000 - registration status: 204

Log output of Eureka-Server:

> Registered instance TEST-SERVICE/<Full Computer Name>:test-service:7000 with status UP

Testing CI using Jenkins