Trabajando con Maven
=====================

Información complementaria
--------------------------
https://github.com/xpoveda/maven-hello-world

https://zeroturnaround.com/rebellabs/maven-cheat-sheet/


Vamos a ello..
--------------
Nos bajamos uno de los repositorios de Github para hacer pruebas con maven.

```bash
git clone https://github.com/xpoveda/maven-hello-world.git
```

Comprobamos la version de maven.
```bash
ubuntu@ubuntu:~/misproyectos/maven-hello-world/my-app$ mvn -version
Apache Maven 3.3.9
Maven home: /usr/share/maven
Java version: 1.8.0_131, vendor: Oracle Corporation
Java home: /usr/lib/jvm/java-8-openjdk-amd64/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "4.10.0-33-generic", arch: "amd64", family: "unix"
```

Mediante el parametro `--quiet` operamos con maven en `silent mode`.
Lo de siempre, `mvn clean` para borrar, `mvn compile` para compilar el **.java** y generar un **.class** o `mvn package` para generar un **.jar** que lo encapsule todo.
La primera vez que ejecutemos el maven nos bajará un monton de dependencias, tantas como nos indique el fichero `pom.xml`.
```bash
ubuntu@ubuntu:~/misproyectos/maven-hello-world/my-app$ mvn clean --quiet
ubuntu@ubuntu:~/misproyectos/maven-hello-world/my-app$ mvn compile --quiet

ubuntu@ubuntu:~/misproyectos/maven-hello-world/my-app$ find . -name "*.class"
./target/classes/com/mycompany/app/App.class
```

Con la instruccion `tree` podemos ver el arbol de directorios tambien desde ssh.
```bash
ubuntu@ubuntu:~/misproyectos/maven-hello-world/my-app$ tree
.
+-- dependency-reduced-pom.xml
+-- pom.xml
+-- src
¦   +-- main
¦   ¦   +-- java
¦   ¦       +-- com
¦   ¦           +-- mycompany
¦   ¦               +-- app
¦   ¦                   +-- App.java
¦   +-- test
¦       +-- java
¦           +-- com
¦               +-- mycompany
¦                   +-- app
¦                       +-- AppTest.java
+-- target
    +-- classes
    ¦   +-- com
    ¦       +-- mycompany
    ¦           +-- app
    ¦               +-- App.class
    +-- generated-sources
    ¦   +-- annotations
    +-- maven-status
        +-- maven-compiler-plugin
            +-- compile
                +-- default-compile
                    +-- createdFiles.lst
                    +-- inputFiles.lst

22 directories, 7 files
```

Y ejecutaremos con el clasico `java -classpath` que tambien se puede abreviar con `java -cp` indicando donde estan las clases y la clase principal.
```bash
ubuntu@ubuntu:~/misproyectos/maven-hello-world/my-app$ java -cp target/classes com.mycompany.app.App
Hello World!
```

Con la instruccion `mvn package` generaremos el jar de ejecucion.
```bash
ubuntu@ubuntu:~/misproyectos/maven-hello-world/my-app$ tree
.
+-- dependency-reduced-pom.xml
+-- pom.xml
+-- src
¦   +-- main
¦   ¦   +-- java
¦   ¦       +-- com
¦   ¦           +-- mycompany
¦   ¦               +-- app
¦   ¦                   +-- App.java
¦   +-- test
¦       +-- java
¦           +-- com
¦               +-- mycompany
¦                   +-- app
¦                       +-- AppTest.java
+-- target
    +-- classes
    ¦   +-- com
    ¦       +-- mycompany
    ¦           +-- app
    ¦               +-- App.class
    +-- generated-sources
    ¦   +-- annotations
    +-- generated-test-sources
    ¦   +-- test-annotations
    +-- maven-archiver
    ¦   +-- pom.properties
    +-- maven-status
    ¦   +-- maven-compiler-plugin
    ¦       +-- compile
    ¦       ¦   +-- default-compile
    ¦       ¦       +-- createdFiles.lst
    ¦       ¦       +-- inputFiles.lst
    ¦       +-- testCompile
    ¦           +-- default-testCompile
    ¦               +-- createdFiles.lst
    ¦               +-- inputFiles.lst
    +-- my-app-1.0-SNAPSHOT.jar
    +-- original-my-app-1.0-SNAPSHOT.jar
    +-- surefire-reports
    ¦   +-- com.mycompany.app.AppTest.txt
    ¦   +-- TEST-com.mycompany.app.AppTest.xml
    +-- test-classes
        +-- com
            +-- mycompany
                +-- app
                    +-- AppTest.class
```

Que se podra ejecutar tambien directamente con `java -classpath` pero ahora contra el jar.
```bash
ubuntu@ubuntu:~/misproyectos/maven-hello-world/my-app$ java -cp target/my-app-1.0-SNAPSHOT.jar com.mycompany.app.App
Hello World!
```

El fichero jar encapsula todas las clases.
```bash
ubuntu@ubuntu:~/misproyectos/maven-hello-world/my-app/target/mira$ jar xvf my-app-1.0-SNAPSHOT.jar


ubuntu@ubuntu:~/misproyectos/maven-hello-world/my-app/target/mira$ ls -lt | more
total 2672
drwxrwxr-x 3 ubuntu ubuntu    4096 Sep 18 14:10 META-INF
-rw-rw-r-- 1 ubuntu ubuntu 2700868 Sep 18 14:10 my-app-1.0-SNAPSHOT.jar
drwxrwxr-x 4 ubuntu ubuntu    4096 Sep 18 14:10 android
drwxrwxr-x 4 ubuntu ubuntu    4096 Sep 18 14:10 com
drwxrwxr-x 3 ubuntu ubuntu    4096 Sep 18 14:10 edu
drwxrwxr-x 9 ubuntu ubuntu    4096 Sep 18 14:10 java
drwxrwxr-x 6 ubuntu ubuntu    4096 Sep 18 14:10 javax
drwxrwxr-x 3 ubuntu ubuntu    4096 Sep 18 14:10 net
drwxrwxr-x 7 ubuntu ubuntu    4096 Sep 18 14:10 org
```

Y ademas contiene un directorio `META-INF` con un fichero `MANIFEST.MF` con informacion sobre la generacion y la clase principal.
Tambien se indica el nombre el `artifact` final y el `pom.xml` que hemos utilizado. 
```bash
ubuntu@ubuntu:~/misproyectos/maven-hello-world/my-app/target/mira/META-INF$ more MANIFEST.MF
Manifest-Version: 1.0
Archiver-Version: Plexus Archiver
Built-By: ubuntu
Created-By: Apache Maven 3.3.9
Build-Jdk: 1.8.0_131
Main-Class: com.mycompany.app.App
```

```bash
ubuntu@ubuntu:~/misproyectos/maven-hello-world/my-app/target/mira/META-INF/maven/com.mycompany.app/my-app$ more pom.properties
#Generated by Maven
#Mon Sep 18 14:10:19 PDT 2017
version=1.0-SNAPSHOT
groupId=com.mycompany.app
artifactId=my-app
```

```xml
ubuntu@ubuntu:~/misproyectos/maven-hello-world/my-app/target/mira/META-INF/maven/com.mycompany.app/my-app$ more pom.xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <packaging>jar</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>my-app</name>
  <url>http://maven.apache.org</url>

  <properties>
      <!-- This property will be set by the Maven Dependency plugin -->
      <annotatedJdk>${org.checkerframework:jdk8:jar}</annotatedJdk>
  </properties>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.checkerframework</groupId>
        <artifactId>checker</artifactId>
        <version>1.9.4</version>
    </dependency>
    <dependency>
        <groupId>org.checkerframework</groupId>
        <artifactId>checker-qual</artifactId>
        <version>1.9.4</version>
    </dependency>
    <dependency>
        <groupId>org.checkerframework</groupId>
        <artifactId>jdk8</artifactId>
        <version>1.9.4</version>
    </dependency>
  </dependencies>
  <build>
    <plugins>
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.2.1</version>
        <executions>
          <execution>
            <goals>
              <goal>java</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <mainClass>com.mycompany.app.App</mainClass>
          <arguments>
            <argument>argument1</argument>
          </arguments>
        </configuration>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <transformers>
                <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                  <mainClass>com.mycompany.app.App</mainClass>
                </transformer>
              </transformers>
            </configuration>
          </execution>
        </executions>
      </plugin>
      <plugin>
          <!-- This plugin will set the properties values using dependency information -->
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-dependency-plugin</artifactId>
          <version>2.3</version>
          <executions>
              <execution>
                  <goals>
                      <goal>properties</goal>
                  </goals>
              </execution>
          </executions>
      </plugin>
      <plugin>
          <artifactId>maven-compiler-plugin</artifactId>
          <configuration>
              <source>1.8</source>
              <target>1.8</target>
              <fork>true</fork>
              <!-- Add all the checkers you want to enable here -->
              <annotationProcessors>
                  <annotationProcessor>org.checkerframework.checker.nullness.NullnessChecker</annotationProcessor>
              </annotationProcessors>
              <compilerArgs>
                  <!-- location of the annotated JDK, which comes from a Maven dependency -->
                  <arg>-Xbootclasspath/p:${annotatedJdk}</arg>
              </compilerArgs>
          </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

