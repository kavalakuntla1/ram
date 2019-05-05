### MAVEN

build tool

to build the code
execute the unit tests
packaged the code into some format

C -- MAKE
JAVA -- ANT, MAVEN, GRADLE
C# and DOTNET -- MSBUILD
NODEJS -- GULP(npm)
Python -- PIP, PyPi

### Maven installation

1. install open jdk 8

sudo apt-get install default-jdk
sudo apt-get install openjdk-8-jdk
java -version
echo $JAVA_HOME
ls /usr/lib/jvm/java-1.8.0-openjdk-amd64
sudo vi /etc/environment
JAVA_HOME="/usr/lib/jvm/java-1.8.0-openjdk-amd64"
source /etc/environment
echo $JAVA_HOME

2. install maven

https://www.vultr.com/docs/how-to-install-apache-maven-on-ubuntu-16-04

sudo apt-get install maven -y
mvn --version
echo $M2_HOME
sudo vi /etc/environment
M2_HOME="/usr/share/maven"
source /etc/environment
echo $M2_HOME

3. install git

sudo apt-get install git -y
git --version


### Maven Steps (in ubuntu)

mkdir projects
cd projects
git clone <git url of any java app like spring petclinic/openmrs/eb-tomcat-snakes/wildrydes>
cd spring-petclinic
1. mvn compile
2. mvn test
3. mvn package


ls
cd targets
ls
java -jar <file.jar>

browser-- publicip:8080

spring petclinic page open

### Maven Steps (in windows)

install java

choco install openjdk

install maven 

choco install maven

mkdir maven
cd maven
git clone <git url of any java app like spring petclinic/openmrs/eb-tomcat-snakes/wildrydes>
cd spring-petclinic
1. mvn compile
2. mvn test
3. mvn package


ls
cd targets
ls
java -jar <file.jar>

browser -- localhost:8080

spring petclinic page open

### maven vs ant

ant - configuration based tool
    - its not dependency management tool
maven - coventional based tool
      - its dependancy mangement tool

### mvn (pom.xml)

POM is an XML file which contains the project configuration details used by Maven. 
It provides all the configuration required for a project

POM means project object model, and, as the name suggests, it defines the model of the project as well

In the normal project development you will add JAR files and libraries as required. 

In Maven-based development, those JAR files, libraries are added to the project using this pom.xml. 
In the pom context we call those JAR files, libraries as dependencies.

# The minimum requirement for a POM are the following:

project root
modelVersion - should be set to 4.0.0
groupId - the id of the project's group.
artifactId - the id of the artifact (project)
version - the version of the artifact under the specified group
 

 artifact - artifacts help describe the function, architecture, and design of software
            artifacts are concerned with the process of development itself
 
 
 after build,test,package in src file we get java folder in both main and test folder

target file is nothing but where you build code

 pom.xml consists of 3 main things

 1. group id - the id of the project's group
 2. artifact id - the id of the artifact (project)
 3. version - the version of the artifact under the specified group


### Maven commands

1. mvn clean - which remove all your target files
2. mvn compile - convert java code into .class
3. mvn test - unit test
             - after compile execute unit test in the form of xml file
4. mvn package - create jar/war file
                - its incremental only changes will build
5. mvn install - to move .M2 folder to target folder(home)
6. mvn deploy - jar file go to repository
              - make available to other systems
7. mvn clean package - full build
                     - build everything


### Maven Lfe cycle
validate - validate the project is correct and all necessary information is available
compile - compile the source code of the project
test - test the compiled source code using a suitable unit testing framework. These tests should not require the code be packaged or deployed
package - take the compiled code and package it in its distributable format, such as a JAR.
verify - run any checks on results of integration tests to ensure quality criteria are met
install - install the package into the local repository, for use as a dependency in other projects locally
deploy - done in the build environment, copies the final package to the remote repository for sharing with other developers and projects.


# day builds 

trigger build and give feedback for every commit
it is periodic process

# night builds

these builds are like automated testing


