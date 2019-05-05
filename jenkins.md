### CI (continuous integration) engine

to execute day builds or night builds manually we use software which does samething 

stack of software to build -- build environment


### Jenkins

- CI tool
- Build in java
- schedular/cron on steroids 
- well designed schedular
- open source
- Any CI tool is schedular
- schedular for CI/CD
- runs any command --> we get a user which jenkins runs anything

### Have to remember
1. give enought permissions to jenkins user (like visudo(sudo) in linux and Administator in windows)
2. command not found
   give path to path variables (environmental variable)
   give full path

plugin --> extension to jenkins (UI extension)
        - give only UI not install softwares
        - convert UI to CLI 

ex:- mvn package (manual extension)
     if you install maven package
     package(UI extension)

try to use as much less plugins as possible

- jenkins runs command on our behalf as <jenkins> user

- To do CI/CD we should know --> all the commands
- except java every s/w we need to install in jenkins

### whenever we install jenkins we should know
1. <default user> name ? ---> jeenkins(while installation)
2. make jenkins a sudo user or add jenkins to admin group
3. jenkins as a linux service(daemon)
4. jenkins is java web app which can run on any application server(tomcat or glassfish)
4. jenkins need atleast java 1.8



### Jenkins Installation

1. Install open jdk8

https://www.digitalocean.com/community/tutorials/how-to-install-java-with-apt-get-on-ubuntu-16-04


sudo apt-get install openjdk-8-jdk
java -version
echo $JAVA_HOME
ls /usr/lib/jvm/java-8-openjdk-amd64
sudo vi /etc/environment
JAVA_HOME="/usr/lib/jvm/java-8-openjdk-amd64"
source /etc/environment
echo $JAVA_HOME

2. Install jenkins

https://www.digitalocean.com/community/tutorials/how-to-install-jenkins-on-ubuntu-16-04#step-1-%E2%80%94-installing-jenkins

https://wiki.jenkins.io/display/JENKINS/Installing+Jenkins+on+Ubuntu

https://pkg.jenkins.io/debian-stable/

wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt-get update
sudo apt-get install jenkins

browser -- publicip:8080
sudo cat /var/lib/jenkins/secrets/initialAdminPassword -- copy
Administrator password -- paste
install suggested plugins
create first admin user
username -- give any
password -- give any
save and continue
jenkins url --> http://publicip:8080
save and finish

jenkins is ready --> start using jenkins



JC ---> first project --> build  --> execute shell --> pwd        --> save --> build now --> console output
                                               whoami
                                               echo $JAVA_HOME


su jenkins
passwd jenkins --> jenkins
su jenkins
pwd(/var/lib/jenkins)
cd jobs(jenkins projects stored in this folder)
ls 
projectfile
cd projectfile
ls
config.xml
cd ..
cd ..
cd workspace(all the work done after in this folder)
ls
projectfile

# if you want run sudo commands 
first give sudo permissions to jenkins user

visudo

jenkins= ALL(ALL:ALL) NOPASSWD:ALL


JC --> second project --> build --> execute shell --> sudo apt-get update --> save --> build now 
                                                      sudo apt-get tree -y
-->console ouput


3. Maven installation

sudo apt-get install maven -y
mvn --version

4. check git is install or not if not istall
git --version
sudo apt-get install git -y

## deploy java app 

git clone <javaapp url like spring petclinic or open mrs>
cd spring-petclinic
mvn package

# firstway
JC--> spring-petclinic--> build --> execute shell --> above 3 commands -->Save build now-->console o/p

su jenkins (/var/lib/jenkins-->jenkins home folder)
cd .m2
ls
cd repository(maven build stores here)
ls

1. first switch to jenkins user(home)
2. go to cd workspace create folder do your work dont messup with default files

after build success of spring petclinic

su jenkins
cd workspace/spring-petclinic/spring-petclinic/target
 we get .jar file

# secondway (using plugin)

JC --> spring-petclinic-1 --> source code management(Git) --> repository url (git clone <spring-petclinic>) --> build --> invoke top level maven targets (package)--> save --> build now --> console o/p 

JC --> game of++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++ life --> source code management(Git) --> repository url (git clone <gameoflife url>) --> build --> invoke top level maven targets (package)--> save --> build now --> console o/p 


## usage of plugin example (changing blue balls to green balls)

JC--> manage jenkins --> manage plugins --> available --> green balls --> install without restart --> restart jenkins

# jenkins home (/var/lib/jenkins)

## build trigger
when ever you control when the build happens (to know chages between git and jenkins)

two ways we can do this (to know changes between git and jenkins)

1. git to jenkins (git sends changes to jenkins)---> github hooks/web hooks trigger for GITScm polling
2. jenikins to git (jenkins ask changes in git) ---> poll scm

git hooks - when we have own git
web hooks - when we have own bitbucket, github
poll scm - jenkins ask git


A, B, C --> 3 projects
A--> downstream-B
B--> upstream-A
c

project can be build sequencially

in jenkins by default individual projects build sequencially 

# build executor 
number  of jobs pararelly executed in the system
how many build can i do on this system+

to increase build executors

JC--> manage jenkins --> configure system --> number of executor --> 4 --> save

executor given to job not build

#to do concurrents builds(execute all at same time) - in reality we wont use these
we use 

Execute concurrent builds if necessary

Throttle builds

# distributed executor using jenkins node

jenkins master connects to jenkins nodes(windows(.net project), redhat,ubuntu,ansible(deploy .jar/.war file) and so on)

jenkins master -- we do less activity
jenkins nodes -- we do more activity

1
## task 
Spring petclinic(git)
DEV branch
any change(commit)
  use Poll SCM

github--spring-petclinic--folk--Lokeshcraig(spring petclinic)
create folder <feb>
git clone <0lokeshcraig(springpetclonic)>
cd spring petclinic
git checkout -b Dev
git push origin Dev

# when team is small (less developers) then we use this

JC--> springpetclinic-daybuild --> SCM(git) --> Repo URL --> branch(Dev) --> Build (invoke top level maven) --> build triggers (poll scm) --> 
* * * * * --> save --> build now --> console output

su jenkins
cd workspace/springpetclinic-build
git branch
commit id of this matched with dev branch commit id in local

to download daybuilds(project(cd workspace/springpetclinic-build/target/*.jar)) and to show test results(cd workspace/springpetclinic-build/target/surefire-reports/*.xml)


JC--> springpetclinic-daybuild --> SCM(git) --> Repo URL --> branch(Dev) --> Build (invoke top level maven(package)) --> build triggers (poll scm) --> 
* * * * * --> post build action (archive the artifacts) --> file to archieve(target/*.jar) --> publish JUnit test results (target/surefire-reports/*.xml) --> save 

do some change in git(spring-petclinic in readme.cmd) 

git status
git add .
git commit -m "test"
git push origin Dev

check JC after 1 minute build starts automatically. check we get .jar file(if you click on this it will download) and test results(if u want to see click on it)
depends on number of commits. if we merge same thing thwice it build thwine it might not be sensible 

# when team is large (we use incremental build like every 15 minutes or 30 minutes or 1 hour)

JC--> springpetclinic-daybuild --> SCM(git) --> Repo URL --> branch(Dev) --> Build (invoke top level maven(clean package)) --> build triggers (build periodically) -->  H/15 * * * * --> save

every 15 minuts build start automatically

vastly used this


# build guide 
will help how to do builds according to scenario
if not write documentation share it to dev team(how to build the code known to them)

in day builds incremental builds are ok
but in night builds incremental builds are not ok here we need full builds

# build enviroments
delete workspace before build starts -- it is used when we need clean maven package
abort the build if struck -- if build is not done with given time it will abort/fail

### fields in jenkins

1. SCM -- where code is
2. build environmnet -- workspace details when your build is fail
3. build triggers -- whenever code changes(poll scm)
                  -- periodic(build periodically)
                  -- script calls 
4. build -- get from dev team
5. post build -- after build what do you want to display or download

So whole reason why we CI is -- give continuous feedback to team our team

### Adminstration of jenkins(manage jenkins)
1. configure systems - configure number of executors
                     - configure email notification(will get in spam of your email)

2. configure global security - by active directory(all windows logs)
                             - LDAP (admin will give logs)
3. configure credentials - to give username & password

above 3 we might not use mostly they are already done

4. global tool configuration - we use mostly
                             - install tools like git, maven rtc
5. reload configuration from disk - reload jenkins from home
6. manage plugins - install plugins(available)
                  - upload plugins(advanced)
                     -- you can upload your company plugins
                     -- files extension supported format .hpi(hudson plugin interface), .jpi(jenkins plugin interface) 
jenkins rest API - we can control jenkins using jsonapi or xmlapi or pythonapi

jenkins interfaces -- UI
                   -- CLI
                   -- Rest API
                       --- Python
                       --- XML
                       --- JSON


# executor
number of paraller projects/jobs which we can build
increase executors



master ---- create nodes(ansible, .net, terraform, kuberbetes, aws cli)
master we use as minimal as possible
what ever work we do we do it in nodes
from master we send jobs to nodes
when master is down just take backup of jenkins home directory
we create labels to each nodes
label is used link nodes from master

# project flow
mvn (requires java) - openmrs-nightbuild(maven)
deploy (requires ansible) - openmrs-deploy-nightbuild(ansible)
selenium test (windows machine) - openmrs-functional Testing-nightbuild(selenium)

openmrs-nightbuild --> post build actions --> openmrs-deploy-nighbuilds(call) ---> openmrs-functional Testing-nightbuild(selenium)(call)

# add multiple nodes to jenkins server(master)
jenkins master and node communication happens via ssh(linux)
                                              via jnlp(windows)

1. from jenkins master to any other linux node
   no password communication(key based authentication)

   jenkins master have keys --- private & public (ssh-keygen)
                            --- public keys of master copy to nodes --- (ssh-copy-id)
2. jenkins master to any windows machine(node)
    jenkins slave.jar (run it as long as it runs communication exit)

    communication direction from node to master here

    global security - enable some ports

in general EC2 machine password based authentication is disabled
   create user <defaultuser>

AWS --> EC2 --> ubuntu 16.04 --> 2 instances(ansible, docker) --> SG(All traffic and SSH) --> Launch

key based authentication

EC2 --> Ansible --> connect
sudo -i
1. create user 
   adduser<jenkins>
   passwd: jenkins
2. visudo
  all members

  jenkins ALL NOPASSWD:ALL

3. vi /etc/ssh/sshd_config
  password authentication = yes

service sshd restart

4. install open jdk 8

sudo apt-get install openjdk
java -version
echo $JAVA_HOME
ls /usr/lib/jvm/java-8-openjdk-amd64
sudo vi /etc/environment
JAVA_HOME="/usr/lib/jvm/java-8-openjdk-amd64"
source /etc/environment
echo $JAVA_HOME

5. install maven
sudo apt-get install maven -y
mvn --version
echo $M2_HOME
sudo vi /etc/environment
M2_HOME:"/usr/share/maven"
source /etc/environment
echo $M2_HOME

EC2--- Jenkins Server --> connect

ssh-keygen

/home/ansible/.ssh

id_rsa
id_rsa.pub

ssh-copy-id node1@<private ip of node1 or private dns of node1>
ssh-copy-id node2@<private ip of node2 or private dns of node2>

ssh <private ip of node 1 or node2>


# manage multiple nodes in build environmet and while doing build how can i send it perticular node

# task 1
jenkins
---> maven java ---> build ---> deploy that using ansible(linux machine) ---> selenium (linux desktop/ laptop)

jenkins master
1. node for linux with maven installed
2. node for linux with ansible

JC --> manage jenkins --> manage nodes ---> add new node --> Name(ansible node private dns name) --> Description (maven build environment) --> executor(1) ---> Remote root directory(/home/jnkins) --> Labels(MAVEN) --> Host (private ip of ansible node) --> credential(add --> jenkins --> username-jenkins --> password-jenkins --> node-jenkins_node--> add --> no key--> jenkins/*******) --> save

JC --> spring-petclinic --> configure --> restrict where this project can be run --> LABEL(MAVEN) --> save


build now

master-- contains all config files are present
node -- contains require files(remote jars, workspace) are present

EC2-->jenkins server
cd logs
ls 
slaves
cd slaves
private ip of ansible node

# task 2

jenkins
--> dotnet project --> build ---> powershell --> selenium  (windows)

jenkins master
1. node for windows with java 8 installed
2. node for windows with java 8 and visual build studio tools

Windows(mylaptop) --> Localdisk:C ---> create folder(Jenkins)

JC --> managing jenkins --> configure global security --> TCP port for JNLP agents (Random)
JC --> manage jenkins --> manage node --> add new node --> Name(mylaptop) --> number of executors(1) ---> remote root directory(C:\Jenkins) --> labels(windows) --> launch method (launch agent via Java Web Start) --> save

mylaptop --> open --> agent.jar --> download
copy url (java -jar agent.jar..............."C:\Jenkins") 

open powershell
---> cd ~
---> cd ~/Downloads
---> paste url with pubilc ip (http://publicip of jenkins server:8080)

mylaptop --> open --> refresh ---> status is up now

AWS --> EC2 ---> Launch instance ---> windows 2016 ---> SG(all traffic) ---> Launch

JC --> create new project --> check --> restrict where project can be run (label --> windows) ---> build(execute windows batch --> echo hello or set) ---> save --> build now

windows(mylaptop) ---> Localdisk:C ---> Jenkins --> workspace --> check (created)

windows(mylaptop) ---> LocalDisk:C --> temp --> create folder(VS)
open visual studio 2017 --> file --> new --> project --> location(C:\temp\VS) --> name(web application)--> OK --> MVC --> OK
windows(mylaptop) ---> LocalDisk:C --> temp --> create folder(VS) --> we get web application folder 
MS Builds format extension .sln or cs
webapplication(VS2017) --> solution webapp --> right click ---> build solution
windows(mylaptop) ---> LocalDisk:C --> temp --> create folder(VS) --> webapp --> packages

to deploy app 

webapplication(VS2017) --> solution webapp --> right click --> publish --> pick publish target --> Folder(bin\Release\Publish) --> Publish

windows(mylaptop) ---> LocalDisk:C --> temp --> create folder(VS) --> webapp --> webapp -->  bin --> Release --> Publish --> we get some content

# do the samething in command line

PS ---> cd "C:\Programming Files\Microsoft Visual Studio\2017\Community\MSBuild\15.0\Bin"
   ---> .\MSBuild.exe \help
   ---> ./msbuild C:\temp\VS\WebApplication\WebApplication.sln

JC --> manage jenkins --> global tool configuration -->  MSBuild --> Name(msbuild) --> Path C:\Programming Files\Microsoft Visual Studio\2017\Community\MSBuild\15.0\Bin


JC --> dotnet1 ---> SCM (git ---> git url <asp-net-sample git>) ---> build (Build a VS project or solution using MSBuild --> MSBuildVersion(msbuild) ---> MSBuild file(PodioAspNetSample.sln)) ---> restrict this project (label --> windows) --> save --> buildnow

if you get error

JC --> manage jenkins --> global tool configuration -->  MSBuild --> Add MSBuild --> Name(msbuild2) --> Path C:\Programming Files\Microsoft Visual Studio\2017\Community\MSBuild\15.0\Bin --> save

JC --> dotnet1 ---> SCM (git ---> git url <asp-net-sample git>) ---> build (Build a VS project or solution using MSBuild --> MSBuildVersion(msbuild2) ---> MSBuild file(PodioAspNetSample.sln)) ---> restrict this project (label --> windows) --> save --> buildnow

# in dot net
we should know how to do "nuget package restore"

PS (C:\programming files.........\bin) --> ./msbuild cd:\Jenikins\workspace\dotnet1\PodioAspNetSample.sln
                                       ---> ./msbuild /restore cd:\Jenikins\workspace\dotnet1\PodioAspNetSample.sln (resolve issue)

speak with dev team to help if we get errors





EC2 ---> Docker node ---> connect --->  ssh jenkins@privatedns of docker node

                     ---> docker --version
                     ---> docker info


EC2 ---> ansible node ---> connect ---> ssh jenkins@privatednsof ansible node

                      ----> ansible --version

JC --> spring-petclinic1 ---> build (executeshell --> ansible --version) --> save ---> build now

or 

# to build 2 projects one after another
JC --> new project(ansibleversion) ---> restrict this project (MAVEN) ---> build (execute shell ---> ansible --version) ---> build trigger(build after other projects are built --> project to watch(spring petclinic1)) --> save

or 

JC --> spring-petclinic1 --> configure ---> post build (build other projects---> project to build(ansibleversion)) ---> save


JC --> spring petclinic1 --> we get Downstream projects (ansibleversion)

JC --> ansibleversion --> we get Downstream projects (spring petclinic1)

windows is not secured way to do using public ip


# case study
1. A and B both are in same machine (we can directly copy)
Job A
    which branch
    how can i change something
    jar (after build we get)

Job B
   jar (copy from Job A)



  EC2 --> Jenkins Server --> start
  JC --> maage jenkins --> manage plugins ---> available ---> buildmonitor view --> install
  JC --> new view --> viewname(monitor) --> build monitor view ---> ok --> select (spring-petclinic and openmrs) --> ok
  JC --> new view --> viewname(my builds) --> list view ---> ok --> select (spring-petclinic and openmrs) --> ok



  JC --> new item --> spring-A --> SCM(git--><git clone spring petcline url>) ---> Build (invoke maven --> package) ---> post build action(artifacts ---> target/*.jar) --> save --> build now

2. A and B are in different machine (we store in common place from there we can copy)

jenkins
    workspace
      A
        target
          my stuff
             ---> common location(S3, SAN/NAS, Repositories)
      B
copy artifacts plugin


# using parameters

JC --> new item --> test --> build --> execute shell( echo Hello) --> general (this project is parameterized --> string parameter --> name (MSG --> Name(Hello))) --> save --> build with parameters --> project test (MSG-->Hello) build --> console output

JC --> new item --> test --> build --> execute shell( echo $MSG) --> general (this project is parameterized --> string parameter --> name (MSG --> Name(Hello))) --> save --> build with parameters --> project test (MSG-->Hello) build --> console output

will display what ever we give it in MSG

# list of environmental variables

JC --> new item --> test --> build --> execute shell( echo $MSG and echo current build number is $BUILD_NUMBER) --> general (this project is parameterized --> string parameter --> name (MSG --> Name(Hello))) --> save --> build with parameters --> project test (MSG-->Hello) build --> console output

display $MSG and build number in output

# to call project with parameters

JC --> Manage jenkins --> manage plugins --> available ---> parameterized trigger ---> install

parameters can be passed in the way of 

KEY = VALUE

MSG_USER = "How are you"
BRANCH_TEST_NAME = "Master"


JC --> new item ---> hello ---> build (execute shell (echo Hello)) ---> post build actions ( trigger parameterized build on other projects --> project to build(test) --> Add parameters(Predefined parameters ---> Parameters(MSG_USER="FROM Hello")) --> save ---> build now 

for hello downstream project is test 
for test upstream project is hello


#### Pipeline
it is configuration as a code
it uses groovy language
groovy is java based language

JC --> new item --> spring-pet --> SCM (git ---> <git url of spring-pet>) --> build (invoke maven--> package) --> post build actions (artifacts --> target/*.jar) --> save --> build now

Pipeline starts with node
# multi node scenario
node ('labels') {

}

inside node we have stages
# single node scenario
node {

stage ('SCM') {
    //git clone
    git 'https://github.com/spring-projects/spring-petclinic.git'
}
stage ('build the packages'){
   
   //mvn package
   sh label: '', script: 'mvn package'
}

state ('artifacts'){

  //archiving artifact
  archive 'target/*.jar'  
}

}


JC --> spring-pipeline --> pipeline --> pipeline script --> 
sample step (git-->git url of spring-petclinic)---> generate pipeline script ---> we get git 'https://github.com/spring-projects/spring-petclinic.git'

sample step (shell script --> mvn package)---> generate pipeline script ---> sh label: '', script: 'mvn package'

sample step (archive artifacts --> target/*.jar)---> generate pipeline script ---> archive 'target/*.jar'
 
sample step (junit: Archive JUnit-formatted test results --> target/surefire-reports/*.xml) junit 'target/surefire-reports/*.xml'

JC --> spring-pipeline --> pipeline --> pipeline script --->                                    save  ----> build now

node {

  stage ('SCM') {
    //git clone
    git 'https://github.com/spring-projects/spring-petclinic.git'
}
  stage ('build the packages'){
                                                                    
    //mvn package
    sh label: '', script: 'mvn package'
}
  stage ('archival'){

   //archiving artifact
   archive 'target/*.jar'  
}
   stage ('test results'){

   //junit test results
   junit 'target/surefire-reports/*.xml'  
}

}

  
stage view (will show logs)--->last successful artifacts(will get spring-petclinic.jar)-->test results(will display test results in .xml file)




# same build steps per branch
github --> create repository(JenkinsPipelineRepo) --> public ---> readme ---> create

F:\Devops\My Practice\Jenkins-Practice\Feb --> git clone <url of JenkinsPipelineRepo> --> cd JenkinsPipelineRepo ---> 
mkdir spring-petclinic --->  open VSC in this folder --> create jenkinsfile --> copy above code
mkdir openmrs ---> open VSC in this folder ---> create jenkinsfile ---> copy above code(url changes)

git status
git add .
git commit -m "added jenkins file"
git push

JC ---> new item --> springpetdev --> pipeline --> advanced pipeline options (Pipeline with SCM --> git --> JenkinsPipelinRepo git url --> jenkins file(spring-pet/Jenkinsfile)) --> save --> build now
# different build steps per branch
copy (Jenkinsfile) from
F:\Devops\My Practice\Jenkins-Practice\Feb\JenkinsPipelineRepo\spring-pet to
F:\Devops\My Practice\Jenkins-Practice\Feb\spring-petclinic

git status (Dev branch in spring-petclinic folder)
git add .
git commit -m "added jenkins file"
git push origin master

JC ---> new item --> springfromnew --> pipeline --> advanced pipeline options (Pipeline with SCM --> git --> spring petclinic git url --> Dev(branch)--> jenkins file(Jenkinsfile)) --> save --> build now


### Code Quality Analysis
code review
some tools which tells suggestions for improving code (static code compilers)

# sonar qube 
supports multiple languages
used to give code coverage
used to code quality and review checks

#code coverage (line coverage)
in your dev code how many lines are touched by test cases or by unit test)

func add (a,b) {
  if (a == 0 & b == 0)    ----> 1
  {
    return 0:             -----> 2
  }
  else {                  -----> 3
    return a+b;           -----> 4
  }
}

total 4 lines

unit test

add (1,1) ---> 3/4 (1,3,4)
add (1,2) ---> 3/4(1,3,4)
add (0,0) ---> 2/4(1,2)
add (1,0) ---> 3/4(1,3,4)
add (0,1) ---> 3/4(1,3,4)

if all 5 possibilities are there then we have 100%

#branch coverage(conditional coverage)

a == 0 && b == 0

a == 0

(a=!0 b=0) (a=0 b=0) (a=0 b=!0) (a=!0 b=!0) --> 4 branches

BC we have tested everything



these improvements and code coverage we have to show to dev team

# sonar qube (code quality)

version - 6.7.4

browser ---> drectdevopsblog ---> sonar qube ---> copy deb [trusted=yes] http://downloads.sourceforge.net/project/sonar-pkg/deb  binary/

AWS --> EC2 --> Instance (Sonar Qube) ---> connect(do following steps)
1. vi /etc/sources.list
last line paste the above line link
deb [trusted=yes] http://downloads.sourceforge.net/project/sonar-pkg/deb  binary/
2. sudo apt-get update
3. sudo apt-get install openjdk
4. sudo apt-get install sonar=6.7.4
5. sudo service sonar start

AWS --> EC2 --> Instance (Sonar Qube) --> copy public ip
browser --> publicip:9000 --> login using admin as username & password

first time it is asking to create token
SQ console ---> my account ---> security ---> generate new token(mytoken) ---> create --> copy

# connect sonar qube to jenkins
1. first install sonarqube scanner

JC --> manage jenkins --> manage plugins --> available --> sonarqube scanner --> install --> restart jenkins

2. JC --> mange jenkins --> configure system ---> name (sonar-6.7.4) URL (publicip:9000 or privateip:9000) security token (paste mytoken)

3. open folder where open mrs is present edit open mrs jenkinsfile with as shown below content
stage('SonarQube analysis') {
    // performing sonarqube analysis with "withSonarQubeENV(<Name of Server configured in Jenkins>)"
    withSonarQubeEnv('SONAR-6.7.4') {
      // requires SonarQube Scanner for Maven 3.2+
      sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.2:sonar'
    }
  }
so now open git on this open mrs edited jenkinsfile location
git status
git add .
git commit -m "added sonar"
git push

4. JC --> new item --> openmrs-sonar ---> pipeline ---> Pipeline SCM --> git (clone url of open mrs) --> file location (openmrs/jenkinsfile) --> save ---> build now

we will get sonarqube view on build history --> if we click on that we are connected to SQ console
we will get sonarqube test results(bugs or code corrections)
if sonarqube builds fails go to sonar quality gate plugin github page(https://github.com/jenkinsci/sonar-quality-gates-plugin/blob/master/README.md) follow the instructions to stop the build


SQ console --> projects --> we get open mrs


### code build storage

in jenkins after build we get 
   --> binaries or artifacts

where we store some where

so possible locations to store
1. shared drive(NAS/SAN)
2. cloud drive (s3, blob storage)
3. proper versioning(artifact repository) 
     => nexus(only java) & 
     => Jfrog(.net, java full pledged repo)



# shared drive
project_name
  build number (using environmental variables in Build(JC))
# s3, Blob storage
1. s3 aws cli -- mostly used
      aws s3 cp
      az blob
2. s3 plugin
# proper versioning
JFrog Artifactory ---> open source

1. install artifactory
2. plugin
3. Configure --> artifactory

#install artifactory

browser --> www.qualitythought.in --> resources --> course material --> devops --> configuring-artifactory.pdf --> follow the link --> install

AWS --> EC2 --> Artifactory --> Copy public ip
browser --> paste public ip(publicip:8081) ---> artifactory cosole open

JFA console --> Admin --> repositories --> local ---> new repo --> select package type(maven)

JC --> manage jenkins ---> manage plugins --> available --> artifactory ---> install ---> restart
JC --> manage jenkins --> configure system ---> artifactory servers --> server id (artifactory) URL (privateip:8081/artifactory) -->username(admin) --> password() ---> test connection(find means ok and error means not ok) 

JC --> new item/project --> testing-artifactory --> maven project --> SCM (git --> url of openmrs) --> build environment(ressolve artifacts from artifactory) --> build (Root POM(pom.xml) and goals & options(package)) --> post build actions(deploy artifacts to artifactory--> target release repo(artifact repo name) -->target snap repo(artifact repo name)) --> save

for free style project --> build environmet(generic artifactory integration --> Artifactory upload server(privateip:8081/artifactory) --> target repo(artifact repo) --> published artifacts(gameoflife-web/target/*.war) -->Artifactory download server(privateip:8081/artifactory)) --> build (invoke top level maven targets --> maven version(Default) --> goals(package)) ---> save --> build now --> we get new option "artifactory build info"
follow the documentation
plugin --> disk usage

# monitoring tool for devops
hygieia
company - capital one use that
we can integrate hygieia with jenkins, jira give in single view
# inside sonarqube console
SQ console --> quality gates(whwther your code is qualified to go to next stage) and Coverage these two are devops engineer role

SQ console ---> quality gates --> create QG --> name(adeventure works)--> create

conditions 
line coverage---> my lc is error(< 90%)
             ---> my line coverage id (95%)  is warning
cognitive complexity 
           ----> <90 (error)
           ---> < 95 (warning)






### Jenkins connection with Ansible

# case:- 1

Jenkins Master

Jenkins node(Ansible)
1. create jenkins as a user
2. install maven
3. install ansible

project folder we must have pom.xml file and ansible folder(consists of ansible-playbook file and inventory file) -- single git repository

commands to be used in build steps

mvn package
ansible-playbook -i invertory ansibleplaybookfile(test.yml)

# case :-2
Jenkins Master

Jenkins Node(Maven(label))
1. create jenkins as a user
2. install maven
git repo --> consists of code

commands to be used in build steps
mvn package

store the build file in s3, shared drive or artifactory

Jenkins Node(Ansible(label))
1. create jenkins as a user
2. install ansible
git repo --> consists of playbook

git clone ansible code

commands to be used in build steps
ansible-playbook -a "build_location=https://s3" -i ansibleplaybookfile(test.yml)



node {

stage ('SCM') {
    //git clone
    git 'https://github.com/openmrs/openmrs-core.git'
}
stage ('build the packages'){
   
   //mvn package
   sh label: '', script: 'mvn package'
}

stage ('artifacts'){

  //archiving artifact
  archive 'target/*.jar'  
}

}
