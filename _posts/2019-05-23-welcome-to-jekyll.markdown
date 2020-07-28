---
layout: post
title:  "Springboot"
date:   2020-07-26 21:03:36 +0530
categories: JAVA Springboot
---

This is a General description of Springboot.

# Overview

## Structure Your Code

Technically, Spring Boot does not require any specific code layout to work. However, there are some best practices that help:

+ Your main application class:

 + It is generally recommend that you locate your main application class in a root package above other
    classes. The `@SpringBootApplication` annotation is often placed on your main class, and it implicitly
    defines a base “search package” for certain items

 + For example, the follwoing structure:

 ```
    com
    +- example
        +- myapplication
            +- Application.java
            |
            +- customer
            |   +- Customer.java
            |   +- CustomerController.java
            |   +- CustomerService.java
            |   +- CustomerRepository.java
            |
            +- order
                +- Order.java
                +- OrderController.java
                +- OrderService.java
                +- OrderRepository.java
 ```
+ Your primary configuration class

 + Although it is possible to use SpringApplication with XML sources, we generally recommend that your primary source be a single `@Configuration` class. Usually the class that defines the main method is a good candidate as the primary `@Configuration`.

 + You can also import configuration classes:

  + The `@Import` annotation can be used to import additional configuration classes. Alternatively, you can use `@ComponentScan` to automatically pick up all Spring components, including `@Configuration` classes.
 + Import XML Configuration

  + If you need to use an XML based configuration, we recommend that you still start with a `@Configuration class`. You can then use an `@ImportResource` annotation to load `XML` configuration files.

  + A single @SpringBootApplication annotation can be used to enable three features, that is:
    + `@EnableAutoConfiguration`: **enable Spring Boot’s auto-configuration mechanism**, which ***imports** many xxxAutoConfiguration classes defined by 
    `@Import(AutoConfigurationImportSelector.class)` *(you will see this when you look at the `@EnableAutoConfigurations` class). More details see the section below.

    + `@ComponentScan`: enable `@Component` scan on the package where the application is located (see the best practices)

    + `@Configuration`: **allow to register extra beans in the context** or **import additional configuration classes**
    SO, the `@SpringBootApplication` annotation is equivalent to using `@Configuration`, `@EnableAutoConfiguration`, and `@ComponentScan` with their default attributes, as shown in the following example:

    ```java
    package com.example.myapplication;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;

    @SpringBootApplication // same as @Configuration @EnableAutoConfiguration @ComponentScan
    public class Application {

        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }

    }
    ```

## Auro-Configuration

You can also choose to use the Spring Boot **auto-configuration**, which attempts to automatically configure your Spring application based on the **jar dependencies** that you have added.

For example, if the H2 database `Jar` is present in the classpath and we have not configured any beans related to the database manually, the Spring Boot’s **auto-configuration** feature automatically configures it in the project.

To enable auto-configuration, you need to add either `@EnableAutoConfiguration` or `@SpringBootApplication` annotations to one of your `@Configuration` classes.

**EnableAutoConfiguration** under the hood:

+ When you click into the `@EnableAutoConfiguration` annotation, you will see that it imports the `AutoConfigurationImportSelector.class`.

+ Within that class, it uses the method `List<String> configurations = SpringFactoriesLoader.loadFactoryNames(..)` to return to your configurations that will be imported into your application.

+ When you click into that method `loadFactoryNames()`, you will see the line: `classLoader.getResources(FACTORIES_RESOURCE_LOCATION)`, where that `Factories_RESOURCE_LOCATION` points to: `\.m2\repository\org\springframework\boot\spring-boot-autoconfigure\2.3.0.RELEASE\spring-boot-autoconfigure-2.3.0.RELEASE.jar!\META-INF\spring.factories`

+ The `xxxAutoConfiguration` classes specified in that `spring.factories` file will be loaded into your application, if conditions specified in those classes are fulfilled (e.g. `@ConditionalOnMissingProperties(..)`)

+ And it is those `xxxAutoConfiguration` classes that configures your Spring Boot Application by reading/injecting values from their respective .properties file. This means that **you can configure those settings in your own `application.properties` file with the correct prefix** 

## Springboot Configuration

### Configuration Files

Springboot apply a global configuration file and this file name is fixed.

+ application.properties
 + syntactic structure: key = value
+ application.yml
 + syntactic structure: key: value

The function of configuration file: modify the default value in Springboot configuration, because Springboot has allocated these values in the background.

### YAML Configuration File

`TAML` is the short for "YAML Ain't a Markup Language". It is a configuration language which can assign value to objects.

For example, to assgn values to the object `Person`:

```java
package com.danny.pojo;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.util.Date;
import java.util.List;
import java.util.Map;

@Component
@ConfigurationProperties(prefix = "person")

//@ConfigurationProperties: To map the values in configuration file to the component.
// And tell SpringBoot to combine the properties in this class with the values in the configuration files
// The parameter prefix = "Person" : To correspond the values in configuration file with the properties of Person object

public class Person {
//    person:
//    name: Danny
//    happy: false
//    birth: 2020/7/11
//    maps: {k1: v1,k2 v2}
//    list:
//     - code
//     - music
//     - sport
//    dog=Dog{name='cat', age=3}
    private String name;
    private Integer age;
    private Boolean happy;
    private Date birth;
    private Map<String,Object> maps;
    private List<Object> lists;
    private Dog dog;

    public Person() {
    }

    public Person(String name, Integer age, Boolean happy, Date birth, Map<String, Object> maps, List<Object> lists, Dog dog) {
        this.name = name;
        this.age = age;
        this.happy = happy;
        this.birth = birth;
        this.maps = maps;
        this.lists = lists;
        this.dog = dog;
    }

    public String getName() {
        return name;
    }

    public Integer getAge() {
        return age;
    }

    public Boolean getHappy() {
        return happy;
    }

    public Date getBirth() {
        return birth;
    }

    public Map<String, Object> getMaps() {
        return maps;
    }

    public List<Object> getLists() {
        return lists;
    }

    public Dog getDog() {
        return dog;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public void setHappy(Boolean happy) {
        this.happy = happy;
    }

    public void setBirth(Date birth) {
        this.birth = birth;
    }

    public void setMaps(Map<String, Object> maps) {
        this.maps = maps;
    }

    public void setLists(List<Object> lists) {
        this.lists = lists;
    }

    public void setDog(Dog dog) {
        this.dog = dog;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", happy=" + happy +
                ", birth=" + birth +
                ", maps=" + maps +
                ", lists=" + lists +
                ", dog=" + dog +
                '}';
    }
}
```
The configuration `YAML` file is demonstrated as the following:

```yml
person:
  name: Danny
  happy: false
  birth: 2020/7/11
  maps: {k1: v1,k2 v2}
  list:
    - code
    - music
    - sport
  dog:
    name: cat
    age: 3
```

In the test class:

```java
package com.danny;

import com.danny.pojo.Dog;
import com.danny.pojo.Person;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class Demo01HelloworldApplicationTests {

    @Autowired
    private Person person;
    @Test
    void contextLoads() {
        System.out.println(person);
    }
}
```

Ths output in the following :

```
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.3.2.RELEASE)

2020-07-28 16:21:27.176  INFO 16576 --- [           main] c.d.Demo01HelloworldApplicationTests     : Starting Demo01HelloworldApplicationTests on 举世无双的暗影精灵 with PID 16576 (started by pc in D:\zwh52\coursework\SpringBoot\Springbootdemo)
2020-07-28 16:21:27.178  INFO 16576 --- [           main] c.d.Demo01HelloworldApplicationTests     : No active profile set, falling back to default profiles: default
2020-07-28 16:21:27.777  INFO 16576 --- [           main] c.d.Demo01HelloworldApplicationTests     : Started Demo01HelloworldApplicationTests in 1.175 seconds (JVM running for 2.81)

Person{name='Danny', age=null, happy=false, birth=Sat Jul 11 00:00:00 CST 2020, maps={k1=v1, k2v2=}, lists=null, dog=Dog{name='cat', age=3}}

Process finished with exit code 0
```
We can find that the vlues in the configuration `YAMl` file has been assigned to the object.
If we only require a single value in the configuration file, we can use `@Value`, if there are many values to be assigned, then `application.yml` configuration file is the first choice.

### Multiple Configuration 

When developing programs, it usually requires multiple running enviornments, such as **developing configuration**, **testing configuration**.

We can use **multi-document module** in `YAML`.

For example, the `YAML` can be seperated by `---`:

```yml

person:
  name: Danny
  happy: false
  birth: 2020/7/11
  maps: {k1: v1,k2 v2}
  list:
    - code
    - music
    - sport
  dog:
    name: cat
    age: 3
dog:
  name: cat
  age: 3
spring:
  profiles:
    active: dev      //this indicate that the executing configuration is "dev" part

 ---
person:
  name: Frank
  happy: true
  birth: 2020/7/22
  maps: {k1: v1,k2 v2}
  list:
    - Math
    - music
    - Reading
  dog:
    name: Dig
    age: 10
dog:
  name: Dig
  age: 10
spring:
  profiles: dev 

```
Then, the output is the following:

```
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.3.2.RELEASE)

2020-07-28 17:04:00.623  INFO 19092 --- [           main] c.d.Demo01HelloworldApplicationTests     : Starting Demo01HelloworldApplicationTests on 举世无双的暗影精灵 with PID 19092 (started by pc in D:\zwh52\coursework\SpringBoot\Springbootdemo)
2020-07-28 17:04:00.624  INFO 19092 --- [           main] c.d.Demo01HelloworldApplicationTests     : The following profiles are active: dev
2020-07-28 17:04:01.012  INFO 19092 --- [           main] c.d.Demo01HelloworldApplicationTests     : Started Demo01HelloworldApplicationTests in 0.77 seconds (JVM running for 1.605)

Person{name='Frank', age=null, happy=true, birth=Wed Jul 22 00:00:00 CST 2020, maps={k1=v1, k2v2=}, lists=null, dog=Dog{name='Dig', age=10}}
Dog{name='Dig', age=10}


Process finished with exit code 0
```

The principle of autoConfiguration:
1. SpringBoot will load huge amount of autoConfigration classes.
2. We can check if the function we need is in default autoConfiguration classes.
3. If the componet we need is in the classes, we do not need to configurate the component mannully.
4. When adding components in the autoConfigurated classes, it will obtain attributes in the properties classes. We only need to point out the properties of the values of these properties.

`xxxAutoConfiguration`: auto-generated configuration class.

`xxxProperties`: The relating attributes in the encapsulated configuration files.



