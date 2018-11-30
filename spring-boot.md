# Inheriting the Starter Parent
To configure your project to inherit from the spring-boot-starter-parent, set the parent as follows:

```
<parent>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-parent</artifactId>
	<version>2.1.0.RELEASE</version>
</parent>
```
With that setup, you can also override individual dependencies by overriding a property in your own project. For instance, to upgrade to another Spring Data release train, you would add the following to your pom.xml:

```
<properties>
	<spring-data-releasetrain.version>Fowler-SR2</spring-data-releasetrain.version>
</properties>
```
## Using Spring Boot without the Parent POM
If you do not want to use the spring-boot-starter-parent, you can still keep the benefit of the dependency management (but not the plugin management) by using a scope=import dependency, as follows:

```
<dependencyManagement>
		<dependencies>
		<dependency>
			<!-- Import dependency management from Spring Boot -->
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-dependencies</artifactId>
			<version>2.1.0.RELEASE</version>
			<type>pom</type>
			<scope>import</scope>
		</dependency>
	</dependencies>
</dependencyManagement>
```
To upgrade to another Spring Data release train, you could add the following element to your pom.xml:

```
<dependencyManagement>
	<dependencies>
		<!-- Override Spring Data release train provided by Spring Boot -->
		<dependency>
			<groupId>org.springframework.data</groupId>
			<artifactId>spring-data-releasetrain</artifactId>
			<version>Fowler-SR2</version>
			<type>pom</type>
			<scope>import</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-dependencies</artifactId>
			<version>2.1.0.RELEASE</version>
			<type>pom</type>
			<scope>import</scope>
		</dependency>
	</dependencies>
</dependencyManagement>
```

## Using the Spring Boot Maven Plugin
Spring Boot includes a Maven plugin that can package the project as an executable jar. Add the plugin to your plugins section if you want to use it, as shown in the following example:

```
<build>
	<plugins>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
		</plugin>
	</plugins>
</build>
```

# Starters
Starters are a set of convenient dependency descriptors that you can include in your application. You get a one-stop shop for all the Spring and related technologies that you need without having to hunt through sample code and copy-paste loads of dependency descriptors. For example, if you want to get started using Spring and JPA for database access, include the spring-boot-starter-data-jpa dependency in your project.

e.g spring-boot-starter, spring-boot-starter-activemq, spring-boot-starter-data-jpa etc.

In addition to the application starters, the following starters can be used to add production ready features:

spring-boot-starter-actuator

# Locating the Main Application Class
We generally recommend that you locate your main application class in a root package above other classes. The @SpringBootApplication annotation is often placed on your main class, and it implicitly defines a base “search package” for certain items. For example, if you are writing a JPA application, the package of the @SpringBootApplication annotated class is used to search for @Entity items. Using a root package also allows component scan to apply only on your project.

If you don’t want to use @SpringBootApplication, the @EnableAutoConfiguration and @ComponentScan annotations that it imports defines that behaviour so you can also use that instead.

## Configuration Classes
Spring Boot favors Java-based configuration. Although it is possible to use SpringApplication with XML sources, we generally recommend that your primary source be a single @Configuration class. Usually the class that defines the main method is a good candidate as the primary @Configuration.

Many Spring configuration examples have been published on the Internet that use XML configuration. If possible, always try to use the equivalent Java-based configuration. Searching for Enable* annotations can be a good starting point.

## Importing Additional Configuration Classes
You need not put all your @Configuration into a single class. The @Import annotation can be used to import additional configuration classes. Alternatively, you can use @ComponentScan to automatically pick up all Spring components, including @Configuration classes.

## Importing XML Configuration
If you absolutely must use XML based configuration, we recommend that you still start with a @Configuration class. You can then use an @ImportResource annotation to load XML configuration files.


> The @SpringBootApplication annotation is equivalent to using @Configuration, @EnableAutoConfiguration, and @ComponentScan with their default attributes, as shown in the following example:

```
@SpringBootApplication // same as @Configuration @EnableAutoConfiguration @ComponentScan
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}

}
```

or below basically same

```
@Configuration
@EnableAutoConfiguration
@Import({ MyConfig.class, MyAnotherConfig.class })
public class Application {

	public static void main(String[] args) {
			SpringApplication.run(Application.class, args);
	}

}
```

# Hot Swapping
Since Spring Boot applications are just plain Java applications, JVM hot-swapping should work out of the box. JVM hot swapping is somewhat limited with the bytecode that it can replace. For a more complete solution, JRebel can be used.

The spring-boot-devtools module also includes support for quick application restarts. See the Chapter 20, Developer Tools section later in this chapter and the Hot swapping “How-to” for details.

> This is to see the code changes instantly on applications.

## Developer Tools
Spring Boot includes an additional set of tools that can make the application development experience a little more pleasant. The spring-boot-devtools module can be included in any project to provide additional development-time features. To include devtools support, add the module dependency to your build, as shown in the following listings for Maven and Gradle:

```
<dependencies>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-devtools</artifactId>
		<optional>true</optional>
	</dependency>
</dependencies>
```

## Excluding Resources for restart
Certain resources do not necessarily need to trigger a restart when they are changed. For example, Thymeleaf templates can be edited in-place. By default, changing resources in /META-INF/maven, /META-INF/resources, /resources, /static, /public, or /templates does not trigger a restart but does trigger a live reload. If you want to customize these exclusions, you can use the spring.devtools.restart.exclude property. For example, to exclude only /static and /public you would set the following property:

```
spring.devtools.restart.exclude=static/**,public/**
```

## Disabling Restart
If you do not want to use the restart feature, you can disable it by using the spring.devtools.restart.enabled property. In most cases, you can set this property in your application.properties (doing so still initializes the restart classloader, but it does not watch for file changes).

if you need to completely disable restart support
```
public static void main(String[] args) {
	System.setProperty("spring.devtools.restart.enabled", "false");
	SpringApplication.run(MyApp.class, args);
}
```
## Using a Trigger File
If you work with an IDE that continuously compiles changed files, you might prefer to trigger restarts only at specific times. To do so, you can use a “trigger file”, which is a special file that must be modified when you want to actually trigger a restart check. Changing the file only triggers the check and the restart only occurs if Devtools has detected it has to do something. The trigger file can be updated manually or with an IDE plugin.

> To use a trigger file, set the spring.devtools.restart.trigger-file property to the path of your trigger file.

## Remote Applications
The Spring Boot developer tools are not limited to local development. You can also use several features when running applications remotely. Remote support is opt-in. To enable it, you need to make sure that devtools is included in the repackaged archive, as shown in the following listing:

```xml
<build>
	<plugins>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
			<configuration>
				<excludeDevtools>false</excludeDevtools>
			</configuration>
		</plugin>
	</plugins>
</build>
```
> Then you need to set a spring.devtools.remote.secret property, as shown in the following example:
```
spring.devtools.remote.secret=mysecret
```
> Enabling spring-boot-devtools on a remote application is a security risk. You should never enable support on a production deployment.

# SpringApplication
The SpringApplication class provides a convenient way to bootstrap a Spring application that is started from a main() method. In many situations, you can delegate to the static SpringApplication.run.

## Web Environment
A SpringApplication attempts to create the right type of ApplicationContext on your behalf. The algorithm used to determine a WebApplicationType is fairly simple:

* If Spring MVC is present, an AnnotationConfigServletWebServerApplicationContext is used
* If Spring MVC is not present and Spring WebFlux is present, an AnnotationConfigReactiveWebServerApplicationContext is used
* Otherwise, AnnotationConfigApplicationContext is used

This means that if you are using Spring MVC and the new WebClient from Spring WebFlux in the same application, Spring MVC will be used by default. You can override that easily by calling setWebApplicationType(WebApplicationType).

It is also possible to take complete control of the ApplicationContext type that is used by calling setApplicationContextClass(…​).

## Accessing Application Arguments
```java
@Component
public class MyBean {

	@Autowired
	public MyBean(ApplicationArguments args) {
		boolean debug = args.containsOption("debug");
		List<String> files = args.getNonOptionArgs();
		// if run with "--debug logfile.txt" debug=true, files=["logfile.txt"]
	}

}
```
## Using the ApplicationRunner or CommandLineRunner

If you need to run some specific code once the SpringApplication has started, you can implement the ApplicationRunner or CommandLineRunner interfaces. Both interfaces work in the same way and offer a single run method, which is called just before SpringApplication.run(…​) completes.

If several CommandLineRunner or ApplicationRunner beans are defined that must be called in a specific order, you can additionally implement the org.springframework.core.Ordered interface or use the org.springframework.core.annotation.Order annotation.

## Application Exit

Each SpringApplication registers a shutdown hook with the JVM to ensure that the ApplicationContext closes gracefully on exit. All the standard Spring lifecycle callbacks (such as the DisposableBean interface or the @PreDestroy annotation) can be used.

In addition, beans may implement the org.springframework.boot.ExitCodeGenerator interface if they wish to return a specific exit code when SpringApplication.exit() is called. This exit code can then be passed to System.exit() to return it as a status code, as shown in the following example:

```java
@SpringBootApplication
public class ExitCodeApplication {
	@Bean
	public ExitCodeGenerator exitCodeGenerator() {
		return () -> 42;
	}

	public static void main(String[] args) {
		System.exit(SpringApplication
				.exit(SpringApplication.run(ExitCodeApplication.class, args)));
	}
}
```

## Admin Features

It is possible to enable admin-related features for the application by specifying the spring.application.admin.enabled property. This exposes the SpringApplicationAdminMXBean on the platform MBeanServer. You could use this feature to administer your Spring Boot application remotely. This feature could also be useful for any service wrapper implementation.

> If you want to know on which HTTP port the application is running, get the property with a key of local.server.port.

> Take care when enabling this feature, as the MBean exposes a method to shutdown the application.

# Externalized Configuration
Spring Boot lets you externalize your configuration so that you can work with the same application code in different environments. You can use properties files, YAML files, environment variables, and command-line arguments to externalize configuration. Property values can be injected directly into your beans by using the @Value annotation, accessed through Spring’s Environment abstraction, or be bound to structured objects through @ConfigurationProperties.

On your application classpath (for example, inside your jar) you can have an application.properties file that provides a sensible default property value for name. When running in a new environment, an application.properties file can be provided outside of your jar that overrides the name. For one-off testing, you can launch with a specific command line switch (for example, java -jar app.jar --name="Spring").

## Configuring Random Values

The RandomValuePropertySource is useful for injecting random values (for example, into secrets or test cases). It can produce integers, longs, uuids, or strings, as shown in the following example:

```
my.secret=${random.value}
my.number=${random.int}
my.bignumber=${random.long}
my.uuid=${random.uuid}
my.number.less.than.ten=${random.int(10)}
my.number.in.range=${random.int[1024,65536]}
```

## Encrypting Properties

Spring Boot does not provide any built in support for encrypting property values, however, it does provide the hook points necessary to modify values contained in the Spring Environment. The EnvironmentPostProcessor interface allows you to manipulate the Environment before the application starts. See Section 76.3, “Customize the Environment or ApplicationContext Before It Starts” for details.

If you’re looking for a secure way to store credentials and passwords, the Spring Cloud Vault project provides support for storing externalized configuration in HashiCorp Vault.

## Multi-profile YAML Documents
You can specify multiple profile-specific YAML documents in a single file by using a spring.profiles key to indicate when the document applies, as shown in the following example:

```
server:
	address: 192.168.1.100
---
spring:
	profiles: development
server:
	address: 127.0.0.1
---
spring:
	profiles: production & eu-central
server:
	address: 192.168.1.120
```
In the preceding example, if the development profile is active, the server.address property is 127.0.0.1. Similarly, if the production and eu-central profiles are active, the server.address property is 192.168.1.120. If the development, production and eu-central profiles are not enabled, then the value for the property is 192.168.1.100.

spring.profiles can therefore contain a simple profile name (for example production) or a profile expression. A profile expression allows for more complicated profile logic to be expressed, for example production & (eu-central | eu-west). Check the reference guide for more details.

If none are explicitly active when the application context starts, the default profiles are activated. So, in the following YAML, we set a value for spring.security.user.password that is available only in the "default" profile:

> YAML files cannot be loaded by using the @PropertySource annotation. So, in the case that you need to load values that way, you need to use a properties file.


# Profiles

Spring Profiles provide a way to segregate parts of your application configuration and make it be available only in certain environments. Any @Component or @Configuration can be marked with @Profile to limit when it is loaded, as shown in the following example:

```java
@Configuration
@Profile("production")
public class ProductionConfiguration {
	// ...
}
```

You can use a spring.profiles.active Environment property to specify which profiles are active. You can specify the property in any of the ways described earlier in this chapter. For example, you could include it in your application.properties, as shown in the following example:

> spring.profiles.active=dev,hsqldb
> You could also specify it on the command line by using the following switch: --spring.profiles.active=dev,hsqldb.

## Adding Active Profiles
Sometimes, it is useful to have profile-specific properties that add to the active profiles rather than replace them. The spring.profiles.include property can be used to unconditionally add active profiles. The SpringApplication entry point also has a Java API for setting additional profiles (that is, on top of those activated by the spring.profiles.active property). See the setAdditionalProfiles() method in SpringApplication.

For example, when an application with the following properties is run by using the switch, --spring.profiles.active=prod, the proddb and prodmq profiles are also activated:

```
---
my.property: fromyamlfile
---
spring.profiles: prod
spring.profiles.include:
  - proddb
  - prodmq
```

# Spring WebFlux
Spring Boot provides auto-configuration for Spring WebFlux that works well with most applications.
The auto-configuration adds the following features on top of Spring’s defaults:
* Configuring codecs for HttpMessageReader and HttpMessageWriter instances (described later in this document).
* Support for serving static resources, including support for WebJars

If you want to keep Spring Boot WebFlux features and you want to add additional WebFlux configuration, you can add your own @Configuration class of type WebFluxConfigurer but without @EnableWebFlux.

If you want to take complete control of Spring WebFlux, you can add your own @Configuration annotated with @EnableWebFlux.


# Spring Security
If Spring Security is on the classpath, then web applications are secured by default. Spring Boot relies on Spring Security’s content-negotiation strategy to determine whether to use httpBasic or formLogin. To add method-level security to a web application, you can also add @EnableGlobalMethodSecurity with your desired settings.

You can change the username and password by providing a spring.security.user.name and spring.security.user.password.

The basic features you get by default in a web application are:

* A UserDetailsService (or ReactiveUserDetailsService in case of a WebFlux application) bean with in-memory store and a single user with a generated password (see SecurityProperties.User for the properties of the user).
* Form-based login or HTTP Basic security (depending on Content-Type) for the entire application (including actuator endpoints if actuator is on the classpath).
* A DefaultAuthenticationEventPublisher for publishing authentication events.

You can provide a different AuthenticationEventPublisher by adding a bean for it.

OAuth2 is a widely used authorization framework that is supported by Spring.

If you have spring-security-oauth2-client on your classpath, you can take advantage of some auto-configuration to make it easy to set up an OAuth2/Open ID Connect clients. This configuration makes use of the properties under OAuth2ClientProperties. The same properties are applicable to both servlet and reactive applications.

# Caching
The Spring Framework provides support for transparently adding caching to an application. At its core, the abstraction applies caching to methods, thus reducing the number of executions based on the information available in the cache. The caching logic is applied transparently, without any interference to the invoker. Spring Boot auto-configures the cache infrastructure as long as caching support is enabled via the @EnableCaching annotation.

## Supported Cache Providers
The cache abstraction does not provide an actual store and relies on abstraction materialized by the org.springframework.cache.Cache and org.springframework.cache.CacheManager interfaces.

If you have not defined a bean of type CacheManager or a CacheResolver named cacheResolver (see CachingConfigurer), Spring Boot tries to detect the following providers (in the indicated order):

* Generic
* JCache (JSR-107) (EhCache 3, Hazelcast, Infinispan, and others)
* EhCache 2.x
* Hazelcast
* Infinispan
* Couchbase
* Redis
* Caffeine
* Simple

If the CacheManager is auto-configured by Spring Boot, you can further tune its configuration before it is fully initialized by exposing a bean that implements the CacheManagerCustomizer interface. The following example sets a flag to say that null values should be passed down to the underlying map:

```
@Bean
public CacheManagerCustomizer<ConcurrentMapCacheManager> cacheManagerCustomizer() {
	return new CacheManagerCustomizer<ConcurrentMapCacheManager>() {
		@Override
		public void customize(ConcurrentMapCacheManager cacheManager) {
			cacheManager.setAllowNullValues(false);
		}
	};
}
```

It might happen that more than one provider is present, in which case the provider must be explicitly specified. Even if the JSR-107 standard does not enforce a standardized way to define the location of the configuration file, Spring Boot does its best to accommodate setting a cache with implementation details, as shown in the following example:

```
# Only necessary if more than one provider is present
spring.cache.jcache.provider=com.acme.MyCachingProvider
spring.cache.jcache.config=classpath:acme.xml
```

When @EnableCaching is present in your configuration, a suitable cache configuration is expected as well. If you need to disable caching altogether in certain environments, force the cache type to none to use a no-op implementation, as shown in the following example:

* spring.cache.type=none
