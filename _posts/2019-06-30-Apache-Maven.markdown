---
layout: post
title:  "Maven"
description: An introduction of Maven and its general use.
date:   2020-07-14 21:03:36 +0530
---

This post describes the fundamental concepts of Apache Maven and the use of dependency control. 

### Overview

 In a nutshell, **Maven** is an attempt to **apply patterns to a project’s build infrastructure** in order to **promote comprehension and productivity** by providing a clear path in the use of best practices. **Maven** is essentially a project management and comprehension tool and as such provides a way to help with managing:

+ Builds
+ Documentation
+ Reporting
+ Dependencies
+ SCMs
+ Releases
+ Distribution

Maven can provide benefits for your build process by **employing standard conventions and practices** to accelerate your development cycle while at the same time helping you achieve a higher rate of success.

### Maven Dependency Controll

The traditional `JAVA` programs consume much bigger disk storage than a `Maven` project, which is due to the huge amouts of `.jar` files in the traditional programs. `Maven` program applies a technique called dependency controll to manage the `.jar` packages to be used in the program. 

The dependency is like a coordinate to describe the location of the `.jar` files in the repository. After locating the `.jar` files in the repository, the programs can share the file, thus saving large memory. 

In a particular `Maven` project, there rendered a `.pom` file, where the dependencies are written.
To download `Maven`, Go to the [official site](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) 

### Maven repository

To configurate the local repository location, go to ../apache-maven-3.5.2/conf/settings.xml

![local repository](https://thumbnail0.baidupcs.com/thumbnail/a9364da52n522c29c9a6e589efe28569?fid=156850721-250528-1121170466074613&time=1595480400&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-rehAdaJGAQhiIFv6RKDMd0xhwWE%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=4744578516992199641&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)
<center>Default Local repository</center>

For the `Maven` projects, the compiler will firstly go to the `pom.xml` to find the dependencies' location in the local repository specified above, if it can not find the source `.jar` file it needs, then it will go to the center repository to download the files it needs. In the center repository, there contains **almost all the open-source source `.jar` files required to exploit programs**. Also, it can also find the souces in the remote repository which can create by a company or a group. If the remote repository does not cantain the sources it needs, then, the remote repository will go to the center repository to download the dource files.

We can configurate the local repository by changing the local repository in the `settings.xml`.

![local repository](https://thumbnail0.baidupcs.com/thumbnail/51a10f29bk93b9b017365b09725a4bde?fid=156850721-250528-222241925351342&time=1595473200&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-IC6T4lpQy4RypqBlxkqEbB991kI%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=4742717448682454951&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)
<center>Change Local Repository</center>

After the configuration, all the `.jar` files it needs will be stored in the `D:/zwh52/softewares2/maven/repository` directory.

### Maven Standard Directory Structure

The standard directory & file structures of a Maven project contains the following structures:

+ Core code session
+ Configuration files session
+ Test code session
+ Test configuration session

To create a standard Maven project, you will need somewhere for your project to reside, create a directory somewhere and start a shell in that directory. On your command line, execute the following Maven goal:

```shell
mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

If you have just installed Maven, it may take a while on the first run. This is because `Maven` is **downloading the most recent artifacts** (plugin jars and other files) into your **local repository**. You may also need to execute the command a couple of times before it succeeds. This is because the remote server may time out before your downloads are complete. Don't worry, there are ways to fix that.

You will notice that the generate goal created a directory with the same name given as the artifactId. Change into that directory.

```shell
cd myapp
```
Under this directory you will notice the following **standard project structure**.

```shell
my-app
|-- pom.xml
`-- src
    |-- main
    |   `-- java
    |       `-- com
    |           `-- mycompany
    |               `-- app
    |                   `-- App.java
    `-- test
        `-- java
            `-- com
                `-- mycompany
                    `-- app
                        `-- AppTest.java
```

In the above structure, the `src/main/java` directory is the **core code session**, `src/main/resources` is the configuration files session, `src/test/java` is the **testing code session**, `src/test/resource` is the **testing configuration files**, and usually, `src/main/webapp` is the webpage sources, `js`, `css`, `pictures` and so on.

Every `Maven project` contains a **`POM.xml`**, where the dependencies are rendered in. The `pom.xml` file is the **core of a project's configuration in `Maven`**. It is a **single configuration file that contains the majority of information required to build a project in just the way you want**. The POM is huge and can be daunting in its complexity, but it is not necessary to understand all of the intricacies just yet to use it effectively. This project's POM is:

```xml
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
     
      <groupId>com.mycompany.app</groupId>
      <artifactId>my-app</artifactId>
      <version>1.0-SNAPSHOT</version>
     
      <properties>
        <maven.compiler.source>1.7</maven.compiler.source>
        <maven.compiler.target>1.7</maven.compiler.target>
      </properties>
     
      <dependencies>
        <dependency>
          <groupId>junit</groupId>
          <artifactId>junit</artifactId>
          <version>4.12</version>
          <scope>test</scope>
        </dependency>
      </dependencies>
    </project>
```
### Build the Project

`Maven` is based around the central concept of a **build lifecycle**, which consists of a number of **phases**. (**Each of these build lifecycles is defined by a different list of build phases**, wherein a build phase represents a stage in the lifecycle.)

For example, the default lifecycle comprises of the following phases (for a complete list of the lifecycle phases, refer to the Lifecycle Reference):

+ **validate** - validate the project is correct and all necessary information is available
+ **compile** - compile the source code of the project
+ **test** - test the compiled source code using a suitable unit testing framework. These tests should not require the code be packaged or deployed
+ **package** - take the compiled code and package it in its distributable format, such as a `JAR`.
+ **integration-test**: process and deploy the package if necessary into an environment where integration tests can be run
+ **verify** - run any checks on results of integration tests to ensure quality criteria are met
+ **deploy** - done in the build environment, copies the final package to the remote repository for sharing with other developers and projects.

At this point, since you haven’t added anthing in the folder, you could directly do

``` shell
mvn package
```
which, for Maven, when a phase is given, **it will execute every phase in the sequence up to and including the one defined**. For example, if we execute the `compile` phase, the phases that actually get executed are:

+ validate
    + generate-sources
    + process-sources
    + generate-resources
    + process-resources
+ compile

In the case where we executed mvn package, the command line will print out various actions, and end with the following:

```shell
...
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  2.953 s
[INFO] Finished at: 2019-11-24T13:05:10+01:00
[INFO] ------------------------------------------------------------------------
```
+ Note: 
    + After you run the mvn package, you will see that it created a new folder **target**, in which there will be a packaged `JAR`file that is the snap-shot of the project (in this case, `Maven` starts with a Hello World java file)

Now, since you see that in the standard lifecycle, **package already included compile phase**, it means we could test the newly compiled and packaged `JAR`:

```shell
java -cp target/my-app-1.0-SNAPSHOT.jar com.mycompany.app.App
```

where,

+ -cp, or CLASSPATH (where the JVM will look for the .class file. In this case, the file is packaged in the my-app-1.0-SNAPSHOT.jar), is used as an option to the Java command that specifies the location of classes and packages which are defined by the user, for example, a list of directories, JAR archives and ZIP archives to search for class files.
+ com.mycompany.app.App is the java file that is created by MAVEN and packaged into target/my-app-1.0-SNAPSHOT.jar

There are two other `Maven` lifecycles of note beyond the default list above. They are:


+ **clean**: cleans up artifacts created by prior builds
+ **site**: generates site documentation for this project

### Maven Project Structure

![Maven Structure](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1595240780955&di=9c47bcdf2cc1c4f23decbd4daca3fee3&imgtype=0&src=http%3A%2F%2Fp.primeton.com%2Fuploads%2Fimage%2F201408%2F121d8f99936a.jpg)
<center>Maven Structure</center>

### The POM

**Project Object Model** or **POM** is the fundamental unit of work in Maven. It is an `XML` file that contains information about the project and configuration details used by Maven to build the project. It contains default values for most projects. Examples for this is the build directory, which is `target`; the **source** directory, which is `src/main/java`; the **test source** directory, which is `src/test/java`; and so on. When executing a task or goal, `Maven` looks for the **POM** in the current directory. It reads the **POM**, gets the needed configuration information, then executes the goal.

Some of the configuration that can be specified in the **POM** are the **project dependencies**, the **plugins** or **goals** that can be executed, the **build profiles**, and so on. Other information such as the **project version**, **description**, **developers**, **mailing lists** and such can also be specified.

#### Super **POM**
The **Super POM** is Maven's default POM. All **POMs** extend the Super POM unless explicitly set, meaning the configuration specified in the Super POM is inherited by the POMs you created for your projects.

#### Minimum POM

The minimum requirement for a POM are the following:


+ **project root**
+ **modelVersion** - should be set to 4.0.0
+ **groupId** - the id of the project's group.
+ **artifactId** - the id of the artifact (project)
+ **version** - the version of the artifact under the specified group

Here's an example:

```xml
    <project>
      <modelVersion>4.0.0</modelVersion>
     
      <groupId>com.mycompany.app</groupId>
      <artifactId>my-app</artifactId>
      <version>1</version>
    </project>
```

A **POM** requires that its **`groupId`**, **`artifactId`**, and **`version`** be configured. These three values form the project's fully qualified **`artifact`** name. This is in the form of `<groupId>:<artifactId>:<version>`. As for the example above, its fully qualified artifact name is `"com.mycompany.app:my-app:1"`.

Also, as mentioned in the first section, if the configuration details are not specified, Maven will use their **defaults**. One of these default values is the **packaging type**. Every Maven project has a packaging type. If it is not specified in the **POM**, then the default value `"jar"` would be used.

Furthermore, you can see that in the **minimal POM** the repositories were not specified. If you build your project using the minimal POM, it would inherit the repositories configuration in the **Super POM**. Therefore when Maven sees the dependencies in the minimal POM, it would know that these dependencies will be downloaded from https://repo.maven.apache.org/maven2 which was specified in the Super POM.

#### Project Inheritance

Elements in the POM that are merged are the following:

+ dependencies
+ developers and contributors
+ plugin lists (including reports)
+ plugin executions with matching ids
+ plugin configuration
+ resources

The Super POM is one example of project inheritance, however you can also introduce your own parent POMs by specifying the parent element in the POM, as demonstrated in the following examples.

For example,

As an example, let us reuse our previous artifact, com.mycompany.app:my-app:1. And let us introduce another artifact, com.mycompany.app:my-module:1.

```xml
    <project>
      <modelVersion>4.0.0</modelVersion>
     
      <groupId>com.mycompany.app</groupId>
      <artifactId>my-module</artifactId>
      <version>1</version>
    </project>
```
And let us specify their directory structure as the following:

```shell
.
 |-- my-module
 |   `-- pom.xml
 `-- pom.xml
```
+ Note: my-module/pom.xml is the POM of com.mycompany.app:my-module:1 while pom.xml is the POM of com.mycompany.app:my-app:1

Now, if we were to turn com.mycompany.app:my-app:1 into a **parent artifact** of com.mycompany.app:my-module:1,we will have to modify com.mycompany.app:my-module:1's POM to the following configuration:

com.mycompany.app:my-module:1's POM:

```xml
    <project>
      <modelVersion>4.0.0</modelVersion>
     
      <parent>
        <groupId>com.mycompany.app</groupId>
        <artifactId>my-app</artifactId>
        <version>1</version>
      </parent>
     
      <groupId>com.mycompany.app</groupId>
      <artifactId>my-module</artifactId>
      <version>1</version>
    </project>
```

Notice that we now have an added section, the **parent section**. This section allows us to specify **which artifact is the parent of our `POM`**. And we do so by specifying the fully qualified artifact name of the parent POM. With this setup, **our module can now inherit some of the properties of our parent `POM`**.

Alternatively, if we want the **groupId** and **/ or the version** of your modules to be the same as their parents, you can remove the **groupId** and / or the **version identity** of your module in its **`POM`**.

```xml
    <project>
      <modelVersion>4.0.0</modelVersion>
     
      <parent>
        <groupId>com.mycompany.app</groupId>
        <artifactId>my-app</artifactId>
        <version>1</version>
      </parent>
     
      <artifactId>my-module</artifactId>
    </project>
```
This allows the module to inherit the **groupId and / or the version** of its parent **POM**.

#### Dependency Scope

Five values are defined in the <Scope></scope> values. It is in charge of the deployment of the dependnecies.

+ **Compile**, the default value, for all phases, is released with your project. 
+ **Provided**, like compile, is expected to be supplied by the `JDK`, container, or user. Such as servlet. jar.
+ **Runtime**, used only at runtime, such as a `JDBC` driver, for run and test phases.
+ **test**, used only during testing, is used to **compile and run test code**.**Will not be released with the project**.
+ **System**, like Provided, needs to explicitly provide the jar containing the dependencies; **`Maven` does not look it up in Repository**.

### Standard Directory Layout


|src/main/java |    Application/Library sources
|src/main/resources|    Application/Library resources
|src/main/filters|  Resource filter files
|src/main/webapp|   Web application sources
|src/test/java| Test sources
|src/test/resources|    Test resources
|src/test/filters|  Test resource filter files
|src/it|    Integration Tests (primarily for plugins)
|src/assembly|  Assembly descriptors
|src/site|  Site
|LICENSE.txt|   Project's license
|NOTICE.txt|    Notices and attributions required by libraries that the project depends on
|README.txt|    Project's readme