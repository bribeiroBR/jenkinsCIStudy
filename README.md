# jenkinsCIStudy
It is using Alura.com examples

This project will take the basic information about continuous integration and Jenkins

Continuous Integration will help us to antecipate possible issues. It means, it will check continuosly the version control server checking the stability of the code, if the tests are running properly and so on.

This double check is made by a software that will manage our continuous integration server. Here we wull use Jenkins


01 - ENVIRONMENT SETUP (using MAC):

1st install the Java JDK 8
- http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
- now setup the $JAVA_HOME variable, using terminal
--- cd ~/
--- touch .bash_profile
--- vim .bash_profile
----- insert this line: export JAVA_HOME=$(/usr/libexec/java_home)
----- save
--- source .bash_profile
--- echo $JAVA_HOME
----- The output should be: /Library/Java/JavaVirtualMachines/jdk1.8.0_161.jdk/Contents/Home

2 - Install the Apache Maven
- https://maven.apache.org/download.cgi
- download the tar.gz file
- extract this file for somewhere at your machine
- move it to the /usr/local/ path = sudo mv $CURRENT_PATH /usr/local/
- give permission to execute the scripts = sudo chmod +x bin/mvn
- run the command in terminal = mvn -v
--- the output should be = Apache Maven 3.5.2 (138edd61fd100ec658bfa2d307c43b76940a5d7d; 2017-10-18T09:58:13+02:00)
Maven home: /usr/local/Cellar/maven/3.5.2/libexec Java version: 1.8.0_161...
- now setup the $M2_HOME variable, using terminal
--- cd ~/
--- vim .bash_profile
----- export M2_HOME=/usr/local/apache-maven/apache-maven-3.1.1
----- export PATH=$PATH:$M2_HOME/bin
----- save
--- source .bash_profile
--- echo $M2_HOME


3 - Installing the Apache Tomcat
- https://tomcat.apache.org/download-90.cgi
- download the tar.gz file
- extract this file for somewhere at your machine
- move the folder to /usr/local/ = sudo mv $CURRENT_PATH /usr/local/
- enter in the apache folder = cd /usr/local/apache-tomcat-9.0.4/
- give permission to execute the scripts = sudo chmod +x bin/*.sh
- start the server = sh bin/startup.sh
- in any browser type = http://localhost:8080
--- you should be able to see the famous apache web page


4 - in the terminal type = git --version
--- the output should be the git vesion and if it is not installed yet, then it will start the instalation



02 - JENKINS (CI SERVER) INSTALLATION AND 1ST ADMIN USER

Download Jenkins here:
http://mirrors.jenkins.io/war/

Put the JENKINS.WAR file in your work folder

run this command via terminal
java -jar jenkins.war --httpPort=8088
--- it will start the Jenkins server and set its access to the port=8088

open the browser using
http://localhost:8088

it will requires a Admin password
--- take the password from your terminal in the installation description

After the password select the option Install Suggested Plugins

Create the 1st Admin user and click on SAVE AND FINISH, not in Continue

Click on Start Using Jenkins



P.S.: To run Jenkins, just type "java -jar jenkins.war --httpPort=8088"



03 - JENKINS BASIC CONFIGURATION

we will use Jenkins to compile our projects, package them up, automatize some tasks... It means, we will need to configure our CI server with our JDK, Maven and so on

Click on Manage Jenkins > Global Tool Configuration


- Add JDK
--- unselect the option Install Automatically
--- put a Label = JDK8 Default or something like this
--- in the Path field paste the output message from the command = echo $JAVA_HOME


- Add MAVEN
--- unselect the option Install Automatically
--- put a Label = MAVEN Default or something like this
--- in the Path field paste the output message from the command = echo $M2_HOME



04 - BUILD AND DEPLOY OUR APP

1 - Download / Connecting with our project repository
- we will use an example app from Alura company
- enter in the folder you usually use for your workspace and clone the project
--- git clone https://github.com/alura-cursos/argentum-web.git

2 - Compiling
- To compile your project just enter in the project folder and type
- mvn compile
- P.S.: as this project is using MAVEN it will compile the project

3 - Creating a package
- To create a package to deploy our project just type
- mvn package
- enter in the folder TARGET and you will see the WAR file there
- P.S.: It will also compile and create our WAR file to deploy it

4 - Web Server Up
- check if your we server is running
- here we are using the Apache Tomcat and we already started it
- if it is not started just access the apache-tomcat folder (/usr/local/apache-tomcat)
- run the command = ./startup.sh
- P.S.: If it is not running just give the run privilegies (sudo chmod +x bin/*.sh)
- Due to check the webserver status just go to your browser and type
--- http://localhost:8080

5 - Deploying our App
- copy the WAR file in the weapps folder of your web server
--- cp $PROJECT_CURRENT_PATH/target/argentum-web.war /usr/local/apache-tomcat-9.0.4/webapps/
- access your app via browser
--- http://localhost:8080/argentum-web/



05 - CREATING OUR 1ST JOB TO BUILD AND DEPLOY AUTOMATICALLY

- Basically we will do all the 5 above steps using Jenkins
- It means, we will configure our Jenkins to
--- Donwload / Connect to our repository
--- Compile and Create our app package
--- Copy our WAR file to our webserver app folder
--- Deploy it

1 - Creating a new Jenkins JOB
--- in the dashbopard, click on CREATE NEW JOB
--- give a name (argentum-web) and select the option Freestyle Project and OK

2 - Connecting to our repository
--- click in the tab called Source Code Management
--- select GIT and paste the URL from git, used to clone our project (https://github.com/alura-cursos/argentum-web.git)
--- Save

3 - Compiling and Creating our package
--- in the Dashboard click on Configure in the left side
--- click in the tab called Build
--- select the option Invoke top-level Maven targets
--- select the option Default and type in the Goals field = clean package
--- P.S.: It will cleanup everything before create our package via Maven

4 - Deploy our App
--- in the Dashboard click on Configure in the left side
--- click in the tab called Build
--- click on the button called Add Build Step
--- Select the option Execute Shell
--- paste the command to copy the WAR file in the weApp server folder
----- cp target/argentum-web.war /usr/local/apache-tomcat-9.0.4/webapps/
--- Save

--- P.S.: Just to make sure our configuration is working let's delete our data from the weApp folder = rm -rf /usr/local/apache-tomcat-9.0.4//webapps/argentum-web*
--- No if we try to access our project via browser it will return 404 = http://localhost:8080/argentum-web/


5 - Build our project
--- in the Jenkins dashboard select the JOB and click on BUILD
--- in the console we will find that the last information was the Copy command.
--- access the app via browser http://localhost:8080/argentum-web/
--- Now the app is back.



06 - DEPLOY CONFIGURATION WITH JENKINS EXTENSIONS

Above we just did our 1st job to compile and deploy our app. But remmeber that our last action ws include manually a shell script to copy the WAR file to our web server. We can do that via Jenkins Plugin directly, using a plugin called Deploy to Container.

Installing the Plugin:
- Manager Jenkins > Manage Plugins > Available
--- Search for Deploy to container Plugin
--- Select and install it without Reboot

Using our Plugin in Our JOB configuration
- Now, let's back to our main page, click on the job and configure it using the plugin.
- In the Configuration JOB page click in the BUILD tab and delete the step added before with the CP command.
- In the option ADD POST-BUILD ACTION, select Deploy war/ear to a container
- in the field WAR/EAR files type the path of your WAR file = target/argentum-web.war
- give a name to your context = argentum-web
- add a container = Tomcat 8.x
- add the URL of your tomcat = http://localhost:8080
- add the credentials
--- select jenkins and in the popup that will open use username = jenkins and password = jenkins.
--- Save it
- select the Jenkins credential you created and save

P.S.: Thos credentials must be created in the tomcat configuration, otherwise if you try to build your JOB it will fail.

Defining Credentials in the TOMCAT web server
-  cd /usr/local/apache-tomcat-9.0.4/conf/
- vim tomcat-users.xml
--- in the end add this code:

<role rolename="manager-gui"/>
<role rolename="manager-script"/>
<role rolename="manager-jmx"/>
<role rolename="manager-status"/>
<user username="jenkins" password="jenkins" roles="manager-gui,manager-script,manager-jmx,manager-status" />
</tomcat-users>

- Now reboot your tomcat in order to apply those configurations
--- cd ../bin/
--- ./shutdown.sh
--- ./startup.sh

- Check in the browser if the tomcat is up again
--- http://localhost:8080
--- should display the tomcat page

P.S. = Now just Build your JOB and it will work fine.



Until here all steps are automated, it means, the code is automatically updated, the deploy is automated and so on. But we still don't have the continuous integration. It means, it is automatically, but it doesn't work continuously. We always have to press the button to build our JOB.



07 - CONTINUOUS BUILD AND NOTIFICATIONS

Basically we can do 1 commit = 1 build.
- it will check if the new commit still compiles, the tests are still working, nothing is broken...

In order to do so, we will need to configure that in our repository server, in this case GIT. Because GIT will communicate our Jenkins when a new commit is pushed.

Configure the communication between GIT and JENKINS
- https://wiki.jenkins.io/display/JENKINS/Git+Plugin#GitPlugin-Pushnotificationfromrepository

It basically says:
- edit your GIT file $PROJECT_GIT_PATH/hooks/post-receive
--- example = argentum/.git/hooks/post-receive
- add this line = curl http://$JENKINS_ADDRESS/git/notifyCommit?url="$YOUR_REPOSITORY"
- example = curl http://localhost:8088/git/notifyCommit?url="https://github.com/alura-cursos/argentum-web.git"
P.S.: Further information can be checked in the https://git-scm.com/book/gr/v2/Customizing-Git-Git-Hooks
P.S.: as we are using JENKINS locally this configuration will not work fro us

Defining the time frame to check the GIT repository status
- Select your job > Click in Configure > Click in the tab called BUILD TRIGGER
- check the option Build Periodically
- use a cron configuration to setup the desired time frame
--- H/15**** (it means, each 15 minutes)

Getting a Notification via email when Build Fails
- Manage Jenkins > Configure System > Email Notification
--- Configure your email data
- Configure your JOB > Select the tab Post-build Actions
--- Add a new Step selecting the type E-mail Notification
----- insert your email and check the box Send e-mail for every unstable build
- Configure the Admin email in Jenkins
--- Manage Jenkins > Configure System > in the Jenkins Location topic add your email in the System Admin Email Address field and save.

In order to check if this Email Notification is working, lets introduce an error in the code or stop the tomcat or any other error. Then just build your job an check if after the fail you are receving the email.



At this point we already have our CI working. It means...
- the code is automatically updated;
- the deploy is automated;
- it is building our JOB for each commit in our Repository (GIT);
- it is building our JOB each 15 minutes;
- it is sending notifications via email



08 - RECEVING A HEADS UP FROM JENKINS WITH AUTOMATED TESTS

It's crucial to receive an information regarding our build quality. So, if after run our tests something is failing, we should know that, before other developers try to pull the new code or before it goes to production. It will save us time and estress.

Important to highlight is Jenkins doesn't run the unit tests, Maven is doing that, mostly using the Maven SureFire plugin. Jenkins will juSt notify us about the result.

We can use Maven plugins such as, SureFire and FailSafe to run our unit and integration tests:
P.S.: Maven SureFire plugin is a nice tool to run the unit tests and Maven FailSafe plugin isa nice tool to run the integration tests
http://maven.apache.org/surefire/maven-surefire-plugin/
http://maven.apache.org/surefire/maven-failsafe-plugin/index.html

We also have Functional Tests or Acceptance Tests and those must be also automated as much as possible. It doesn't matter which automation we are using for Functional Tests, all of them will execute the following steps:

- Start Tomcat
- Deploy the WAR file
- Open the browser
- Go to the desired page
- Interact with our app

ARQUILIAN AND ITS ANNOTATIONS
Due to make our life easy, we will use a framework called Arquillian.
- Arquillian is an innovative and highly extensible testing platform for the JVM that enables developers to easily create automated integration, functional and acceptance tests for Java middleware.
- In other words, it is a framework that setup a webserver to run our tests.
- http://arquillian.org/

The Arquilian framework will help us out to perform the first 2 steps above mentioned (Start Tomcat and Deploy the WAR).

Just add the following annotation in your test class:

@RunWith(Arquilian.class)
- public class $TestClassName { ...
- it is a Arquilian annotation.
- It will tell the JUnit or TestNG or the test framework you are using that it will use Arquilian

@Deployment
public static WebArchive $METHOD_NAME_TO_CREATE_DEPLOY
- it is a Arquilian annotation
- it will annotate this method and inside this will describe how to make the deploy and wich file should be deployed
- inside this we will use the ShrinkWrap to create the package to deploy our app

P.S.: After this 2 steps we will solve our 2 first steps, it means, Start Tomcat and Deploy the WAR file

JUNIT / TESTNG ANNOTATIONS
@Before
- it is a JUnit annotation to do something before run the tests
- mostly it will be used to setup our browser, and data before our tests
- It will solve our 3rd line, Open the browser

@Test
- it is a JUnit annotation to execute our test
- it will solve our 2 last lines, Got to desired page and Interact with our app

MAVEN DEPENDENCIES AND ITS STRUCTURE
The pom.xml file is structured in a way to allow us to use Arquilian, JUnit and execute our tests.
It is basically using the dependencies and the profile tag

Maven PROFILE tag
- it will help us to group all the necessary configuration for an especific profile
- let's create a profile to group our Integration Tests' configuration
--- <profiles>
        <profile>
            <id>testes-integracao</id>
- Inside this profile we will describe every thing we need to run our integration tests. Arquilian dependencies, ShrinkWrap, FailSafe, and also how our build should be built.
- To make it more clear, just check the maven lifecycle
https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html#Lifecycle_Reference

Example:
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
<modelVersion>4.0.0</modelVersion>
    <groupId>br.com.caelum.argentum</groupId>
    <artifactId>argentum-web</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>war</packaging>
    <properties>...</properties>
    <dependencies>...</dependencies>
    <build>...</build>
    <profiles>
        <profile>
        <id>testes-integracao</id>
        <dependencies>
            <!--  arquillian-tomcat  -->
            <dependency>...</dependency>
            <dependency>...</dependency>
            <!--  tomcat  -->
            <dependency>...</dependency>
            <dependency>...</dependency>
            <dependency>...</dependency>
            <!--  arquillian testing  -->
            <dependency>...</dependency>
            <dependency>...</dependency>
            <dependency>...</dependency>
            <dependency>...</dependency>
            <!--  org.jboss.arquillian  -->
            <dependency>...</dependency>
            <dependency>...</dependency>
            <dependency>...</dependency>
        </dependencies>
        <build>
            <plugins>
                <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-failsafe-plugin</artifactId>
                <version>2.19.1</version>
                <configuration>...</configuration>
                <executions>
                    <execution>
                        <goals>...</goals>
                    </execution>
                </executions>
                </plugin>
            </plugins>
        </build>
        </profile>
    </profiles>
</project>

JENKINS JOB CONFIGURATION TO RUN THE FUNCTIONAL TESTS
- basically the important point is to set in the goal field the commands you want to run and also the profile you want to run in the build tab of the job
- Goals: clean verify -P$profile_name -Dwebdriver.chrome.driver=$chrome_driver_path_in_your_machine
- -P$profile_name = described in Maven profile tag
- -Dwebdriver.chrome.driver=$chrome_driver_path_in_your_machine = the path where your chromedriver is saved

- Example: clean verify -Pteste-integracao -Dwebdriver.chrome.driver=/user/local/chromedriver


IF MY ENVIRONMENT DOESN'T HAVE A UI BROWSER SUPPORT, LIKE A SERVER
- Use the PhantomJS
http://phantomjs.org/
- PhantomJS is a headless WebKit scriptable with a JavaScript API. It has fast and native support for various web standards: DOM handling, CSS selector, JSON, Canvas, and SVG.

- Example: clean verify -P$profile_name -Dphatomjs.binary.path=$phatomjs_path_in_your_machine

So, basically, about Functional Tests and Jenkins...
- Arquillian Core = give to JUnit or TestNG the availability to put server up to run our tests
- Arquillian Tomcat = a module to use Tomcat.
- ShrinkWrap = create the package to allow us to execute our tests to Arquillian
- Selenium WebDriver = used to create automated tests;
- PhantomJS = emulate a browser without a UI interface
- PhantomJSDriver = used by Selenium to interact with PhatomJS
- SureFire = Maven plugin to generate the unit tests reports
- Failsafe = Maven plugin to execute Integration tests (the tests must end with IT in the test class name)



09 - JENKINS AND QUALITY REPORTS USING SONARQUBE

First of all just check in the Maven pom.xml configuration which reports are configured. We will be able to find out the following reports:
- PMD - code quality analyser report
- SureFire - unit tests report
- JaCoCo - test coverage report

In the end of the Jenkins build construction process, the reports can be found inside the following path WORSPACE/TARGET/SITE/

In order to aggregate all those report's information we will use the SonarQube.
- SonarQube provides the capability to not only show health of an application but also to highlight issues newly introduced. With a Quality Gate in place, you can fix the leak and therefore improve code quality systematically.
https://www.sonarqube.org/

P.S.: There are also some other maven plugins that can be used regarding quality
- FindBugs - code analyser
- CheckStyle - code analyser
And many other options.

SONARQUBE INSTALLATION AND STARTING
- download from the web site
- enter in the BIN folder from SonarQube and execute = ./sonar.sh start
- in the browser you can access = http://localhost:9000 (default port for Sonar)

SONARQUBE AND JENKINS INTEGRATION
- due to make all the information from the code analysers, unit / integration test report plugins and test coverage plugins go to sonarQube we will need another tool called Sonar Scanner

- INSTALLATION ON JENKINS
--- go to Jenkins in the Configuration > Manage Plugin > Available and add the plugin called SonarQube Plugin

- CONFIGURATION ON JENKINS
--- after the installation back to Manage Jenkins > Configure System > Search for Sonar
--- give a name to your sonar = SonarQube
--- serverURL = http://localhost:9000
--- check the field to Enable injection
--- Save
P.S.: At this point the Sonar server was configured, but we still need to tell Jenkins

--- Back to Manage Jenkins > Global Tool Configuration
--- Search for the SonarQube Scanner > give a name = SonarQube Scanner
--- Check the field to install automatically
--- Save and back to your Dashboard
P.S.: Now everything is almost set. We just need to configure our JOB

--- Click in your job > Configure > Build Environment tab
----- Check the field Prepare SonarQube Scanner environment
--- Select the Build tab and in the Goals field just add = sonar:sonar
P.S.: Now our Goals field should be something like this:
clean package sonar:sonar -Ptestes-integracao -Dphantomjs.binary.path=$PATH_TO_PHANTOMJS/bin
--- Save

From this point on, everything is all set, it means, Sonar server running, linked to Jenkins and reciving data correctly


CHECKING THE JENKINS - SONAR DATA INTEGRATION AFTER RUN A JOB
- On Jenkins click on BUILD your project and after finish with Success, back to the JOB screen
- Now you have a SonarQube button and also a SonarQube Quality Gate label with the project name and a flag = OK
- Go to your Sonar server = http://localhost:9000
- in the Menu called PROJECT you will find all the report information, just click in the Project Name and a report screen will open with all metrics



UNTIL HERE WE HAVE OUR CONTINUOUS INTEGRATION SERVER RUNNING. IT IS RESPONDING IN A PRO ACTIVILY WAY:
- BUILDING AND DEPLOYING
- WITH AUTOMATIC JOBS
- EMAIL NOTIFICATIONS
- WITH QUALITY REPORTS, INTEGRATING WITH ARQUILLIAN, PMD CODE ANALYSER, SUREFIRE, FAILSAFE AND SONAR

BUT EVERYTHING IS RUNNING BASED ON OUR MASTER AND NOT USING A BRANCH



10 - JENKINS AND MASTER / BRANCH WORKING

For many reasons working with master is not the best way to work, then let's start to know how to configure our Jenkins to make the CI using branches.

- On Jenkins, click on your JOB and Configure
- Select the tab Source Code Management
- In the GIT configuration part there is a field called BRACHES TO BUILD
- if you let this field empty, it will build your job for any branch

P.S.: It means, Jenkins will execute 1 job for each branch you have. The problem is, in this case you are not integrating the code, you are just testing the branches in an isolated way.

To solve the probelm above mentioned we will ask Jenkins to merge our branch and then build our JOB, then we will have the righ view.

MERGE BRANCHES BEFORE BUILD

- In Jenkins, select your JOB > Configure and select the option Source Code Management
- Below the Branches to Build option there is a field to Additional Behaviours
- Click in Add
--- It will ask the repository's name (you can add a repository' s name by click in the Advanced button in the repository area above this field)
--- write the repository's name
--- in the field Branch to merge to, put the name of the branch you want to merge your code
P.S.: We ca use the master here, but usually there is a Branch called Integration for this case

- just add a POST BUILD action to PUSH this changes to your GIT
--- Select in the Add Post Build Action the option GIT PUBLISHER
--- Check the option Push Only if Build Succeeds
--- Check the field Merge Results

P.S.: One important configuration we are still missing is to add the repository credentials. Because now, as we want to PUSH something we will need to add this.
- In the Source Code Management > Repositories > Click in add > Add your repository credentials > Save and select the credential you just added.
- Save

Build your JOB, by clicking in the BUILD NOW

P.S.: For sure your build now will fail, because we don;t have permission to commit in the ALURA repository

But that is it.
