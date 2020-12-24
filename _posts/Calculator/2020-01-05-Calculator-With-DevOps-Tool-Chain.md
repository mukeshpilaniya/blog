
DockerHub Profile: [https://hub.docker.com/r/pilaniya1337/calculator](https://hub.docker.com/r/pilaniya1337/calculator) <br>
GitHub Profile:[https://github.com/mukeshpilaniya/calculator](https://github.com/mukeshpilaniya/calculator)

### Required Tools: -
- Git (source code management)
- Docker (container node)
- Eclipse /IntelliJ (Project IDE)
- Jenkins (Continuous Integration: git, Continuous testing: Junit)
- Maven (Continuous Build)
- Rundeck (continuous deployment)
- ELK (elastic search, Logstash, Kibana: continuous monitoring)

Installing Git: - ``sudo apt-get install git``

Installing Docker: -
```console
1. sudo apt-get update
2. sudo apt install apt-transport-https ca-certificates curl software-properties-common
3. curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add
4. sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
5. sudo apt update
6. sudo apt install docker-ce
7. sudo usermod -aG docker ${USER}
```
Installing Eclipse IDE:- [https://www.eclipse.org/downloads/](https://www.eclipse.org/downloads/)

Installing Jenkins: -
1. Download the war file
[http://mirrors.jenkins.io/war-stable/latest/jenkins.war](http://mirrors.jenkins.io/war-stable/latest/jenkins.war)
2. Run the war file
 `java -jar jenkins.war`
3. Got to url [http://localhost:8080](http://localhost:8080/)

Jenkins Plugins: -
1. Rundeck plugin
2. Docker plugin
3. Logstash plugin

Configure plugin: - Logstash plugin automatically create calculator index in elasticsearch

![](RackMultipart20201224-4-n1v06t_html_70cf81955190c13a.png) ![](RackMultipart20201224-4-n1v06t_html_e2ab1cdc7f4f2c8f.png) ![](RackMultipart20201224-4-n1v06t_html_6478d60a269d534d.png)

Installing Maven: -
1. `sudo apt install maven`
2. `mvn -version`

Installing Rundeck: -

1. Download rundeck from
https://download.rundeck.org/deb/rundeck\_3.2.6.20200427-1\_all.deb

2. install using dpkg
`dpkg -i rundeck\_3.2.6.20200427-1\_all.deb`
3. Rundeck start and stop command
`sudo service rundeckd start`
`sudo service rundeckd stop`
4. Default address, username, password
Default address: http://localhost:4440
default username and password: admin
5. Allow rundeck to execute sudo commands on system terminal without password enter super user mode
5.1. open file visudo
`sudo visudo`
5.2. Add following lines at end of the file
`rundeck ALL=(ALL) NOPASSWD: ALL`
`Localhost ALL=(ALL) NOPASSWD: ALL`

Create a new Project and job in Rundeck:-

1. Go to the url [http://localhost:4440](http://localhost:4440/) and create a new project name as calculator and save it.

![](RackMultipart20201224-4-n1v06t_html_9f07076428ced964.png)

2. Create a new job in the same project, enter jobname and job group

![](RackMultipart20201224-4-n1v06t_html_7cbaaf6bc4d0f8a5.png)

3. Navigate to workflow section and add the following commands to execute on registered node
3.1. command 1 to replace old jar file with new jar file
``sudo docker cp /var/lib/jenkins/workspace/Calculator\ Build/target/Calculator.jar calculator:/``
3.2. Command 2 to start calculator container
``sudo docker start calculator``
3.3. Command 3 to pass argument to calculator jar file
``sudo docker exec -t calculator java -cp calculator.jar org.iiitb.calculator.App 3 4 2``

4. Under Nodes select execute locally because all these commands will be executed on the local system.
 ![](RackMultipart20201224-4-n1v06t_html_b6abd021d735b673.png)

5. Make note of UUID for future reference and save the job.

![](RackMultipart20201224-4-n1v06t_html_f67fd557118763ed.png)

ELK Installing: -

Step 1 installing elasticsearch: -

1. Set java 8 as default java versionAllow rundeck to execute sudo commands on system terminal without password
2. enter super user mode
3. open file visudo
 `sudo visudo`
5. Add following lines at end of the file
 `rundeck ALL=(ALL) NOPASSWD: ALL`
`Localhost ALL=(ALL) NOPASSWD: ALL`
6. download Elasticsearch followed by public signing key
`sudo wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -`

7. install the apt-transport-https package
`sudo apt-get install apt-transport-https`

8. Add the repository:
``echo "deb https://artifacts.elastic.co/packages/6.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-6.x.list``

9. Update the repo list and install the package:
`sudo apt-get update`
`sudo apt-get install elasticsearch`

10. Update the repo list and install the package
`sudo vim /etc/elasticsearch/elasticsearch.yml`

11. Uncomment &quot;network.host&quot; and &quot;http.port&quot;. Following configuration should be added:
network.host: localhost
http.port: 9200

12. Start ElasticSearch
`sudo systemctl start elasticsearch.service`

Step 2 installing kibana:-

1. Let&#39;s start installing Kibana now and modify Kibana settings
`sudo apt-get install kibana`
`sudo vim /etc/kibana/kibana.yml`

1. Uncomment following lines:
server.port: 5601
server.host: &quot;localhost&quot;
elasticsearch.url: [http://localhost:9200](http://localhost:9200/)

1. start Kibana service
`sudo systemctl start kibana.service`

1. Goto [http://localhost:5601](http://localhost:5601/)

Step 3 installing logstash: -

1. sudo apt-get install logstash
2. sudo service logstash start
3. Got to http://localhost:4440
http://localhost:9200

SDLC: -

Development Phase: - The development of this project is happened in java and it is a maven-based project. The src/main/java directory contains the project source code and the src/test/java directory contains the test cases like unit testing.

![](RackMultipart20201224-4-n1v06t_html_e9de2daf2e9e0e7e.png)

The next step is executing these commands: -

`mvn clean` - command attempt to clean target folder files that are generated during the build by maven
`mvn package` - command convert the entire maven project into an executable jar package

Pom xml file: - To perform unit testing we have to add Junit dependency and maven-jar-plugin for creating a package. It will create a package with a name calculator.

![](RackMultipart20201224-4-n1v06t_html_7bbc065937d564e2.png) ![](RackMultipart20201224-4-n1v06t_html_45bc22554f0df58f.png)

After executing these commands, a target folder is generated automatically which contains our artifacts file calculator.jar. To test this artifact, copy this artifacts file and run the below command in the same directory.

`java -cp calculator.jar org.iiitb.calculator.App`
 org.iiitb.calculator is a package name and App is a class name where calculator methods are defined.

Docker file: - create a &#39;Dockerfile&#39; under project level (at same level of pom.xml)
```dockerfile
# Start with a base image containing Java runtime
FROM openjdk:8
# Add Maintainer Info
LABEL maintainer="github.com/mukeshpilaniya"
# Add a volume pointing to /tmp
EXPOSE 8080
# Add the application's jar to the container
ADD /target/calculator.jar calculator.jar
# Run the jar file
ENTRYPOINT ["java","-cp","calculator.jar","org.iiitb.calculator.App"]
```

The next step is, create a repository(calculator) in github and push project code into calculator repository. The following set of commands will push the code into github repository.

git init
git remote add origin &quot;_[_https://github.com/mukeshpilaniya/calculator.git_](https://github.com/mukeshpilaniya/calculator.git)_&quot;
git add .
git commit -m &quot;initial commit&quot;
git push origin master.

![](RackMultipart20201224-4-n1v06t_html_8b39520b5a9d086b.png)

Build a docker Image: - Enter the following command in the terminal with the home directory of project
`sudo docker build -t calculator\_image .`
This command will create project specific docker image(calculator\_image), now create a container of this image using the following command
`sudo run --name calculator\_container -d calculator\_image`

Software Development life cycle: - The whole project is developed following the DevOps model and using various tools. The software development Life Cycle of this project includes six stages

1. Source Control Management (git)
2. Code Building (maven)
3. Code testing (Junit)
4. Build and Publish Docker image (Docker)
5. Deploying (Rundeck)
6. Monitoring (ELK)

Setup jenkins Pipeline: -
1. Job1: Calculator SCM: - SCM stands for source code management and used for managing the source code of the application, for this project source code is stored in a git repository hosted on GitHub at mukeshpilaniya/calculator. Here we are using pollSCM which checks the git repository after an interval and if there is a change in the code it triggers the pipeline otherwise it doesn&#39;t do anything.
Create a FreeStyle project names as calculator SCM and following is the configuration in this step
![](RackMultipart20201224-4-n1v06t_html_593a36ea541acd.png)
![](RackMultipart20201224-4-n1v06t_html_65dfb79102fb8d7c.png)
![](RackMultipart20201224-4-n1v06t_html_d57afc5108099503.png)

2. Job2: Calculator Build: - This step build triggers automatically when the first job is finished and it will build a jar file in the jenkins working directory if the build is successful it will automatically trigger the calculator Test job. Create a maven project name as calculator Build and following is the configuration in this step.
![](RackMultipart20201224-4-n1v06t_html_1aadc614773b6fb1.png)
![](RackMultipart20201224-4-n1v06t_html_53b42bc2747e9d48.png)
![](RackMultipart20201224-4-n1v06t_html_3710d75579c680f7.png)
![](RackMultipart20201224-4-n1v06t_html_16552df7f1e1d7b3.png)

3. Job3: CalculatorTest: - If the build is successful then the calculator test job will automatically be triggered. it will build the docker image and push it into dockerhub. This job will run test cases and send the control to the calculator deploy. Create a maven project name as calculator Test and Configuration of this step is as follows.
Create a Maven Project and name its calculator Test
![](RackMultipart20201224-4-n1v06t_html_8db04d166cd679c7.png)
![](RackMultipart20201224-4-n1v06t_html_76697942be36ed5.png)
![](RackMultipart20201224-4-n1v06t_html_3e6f013581524c98.png)
![](RackMultipart20201224-4-n1v06t_html_bbb37beb7ce97308.png)

4. Job4: calculator deploy: -This job will automatically trigger if the calculator test job is successfully executed and it will trigger the specified rundeck job.
Create a FreeStyle project and name it as a calculator deploy. Configuration of this step is as follows.
Rundeck instance: rundeck
Copy the job UUID in job identifier id in Post Build Actions → Rundeck
![](RackMultipart20201224-4-n1v06t_html_bbe8fdde32ef35f8.png)
![](RackMultipart20201224-4-n1v06t_html_c8838bf695ee80b7.png)

Create Pipeline View: -
Click on + icon and do following configuration
![](RackMultipart20201224-4-n1v06t_html_56e8a64a131641e3.png)

Click Ok and select calculator SCM as Initial Job under Pipeline Flow. Then click save.
![](RackMultipart20201224-4-n1v06t_html_99335035b4ce8684.png)

Pipeline View Layout: -
![](RackMultipart20201224-4-n1v06t_html_ffa1b9abb2c0a5a1.png)

Create index in kibana and Visualize through graph: -
1. Go to url [http://localhost:5601](http://localhost:5601/)

2. To create kibana index pattern navigate to Management-\&gt;under kibana section choose index pattern and create new index pattern name as &quot;calculator\*&quot;. Click on next step and choose @Timestamp options
![](RackMultipart20201224-4-n1v06t_html_3e549d46a5116d41.png)

1. To see the log navigate to discover-> select calculator as index pattern
![](RackMultipart20201224-4-n1v06t_html_9209ae05577c83de.png)

1. To visualize logs navigate to Visualize-\&gt;click on + icon -\&gt;select one of map type-\&gt;select calculator\* index -\&gt;select appropriate fields-\&gt;click on run-\&gt;save -\&gt;name as calculator 1
Create 3-4 graph and save as calculator 1, calculator 2, calculator 3
![](RackMultipart20201224-4-n1v06t_html_105e13de8ca3219c.png)

1. To create a Dashboard of calculator, navigate to Dashboard \&gt;click on add-\&gt;in search bar type calculator it will show calculator 1,2 and 3 select all of these and save the dashboard name as Calculator.
![](RackMultipart20201224-4-n1v06t_html_18e23dd4b1a09902.png)

Results and execution: - When new features are introduced in a project, then from its building, testing, deployment and monitoring is done in an automated manner.

We first write code for addition method and latter add subtract, multiplication and division method. output of each method as we added in our project code

![](RackMultipart20201224-4-n1v06t_html_df2f4f5f7c9b377c.png)

![](RackMultipart20201224-4-n1v06t_html_14a811037435a557.png)

![](RackMultipart20201224-4-n1v06t_html_19e6131422ab2e0f.png)

![](RackMultipart20201224-4-n1v06t_html_a305758f53bc760b.png)

Conclusion: - DevOps tools help in automating the task of building, testing, releasing, deploying, operating and monitoring in a convenient and efficient way with enormous speed. Manual intervention prone to errors but automated environments are not. Data sharing techniques are used effectively to connect Devs with Ops.

References: -
[1] GitHub project: [https://github.com/mukeshpilaniya/calculator](https://github.com/mukeshpilaniya/calculator)
[2] DockerHub profile: [https://hub.docker.com/u/pilaniya1337](https://hub.docker.com/u/pilaniya1337)
