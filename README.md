# Continuous Integration for Vprofile Project with Vagrant, Jenkins, Nexus and Sonarqube Servers

This repository provides a Vagrant configuration and a pipeline script that automate the setup and deployment of a Java web application. With these files, you can easily create a multi-VM environment and automate the entire CI pipeline.

## Prerequisites
This repository contains a Vagrantfile that defines a multi-machine environment for setting up
Jenkins, Nexus, and SonarQube servers.
Before running Vagrantfile, you need to install the following tools on your machine:

- [VirtualBox](https://www.virtualbox.org/)
- [Vagrant](https://www.vagrantup.com/)
- install vagrant plugins:
 $ vagrant plugin install vagrant-hostmanager
 $ vagrant plugin install vagrant-vbguest

## Usage

1. Clone the repository: `git clone https://github.com/yusuf-inci/ci-local-vprofile.git`
2. Navigate to the project directory: `cd ci-local-vprofile`
3. Start the Vagrant virtual machine: `vagrant up` 
 - This will create three virtual machines, each with its own IP address:
    * JenkinsServer (IP: 192.168.56.11)
    * NexusServer (IP: 192.168.56.21)
    * SonarServer (IP: 192.168.56.31)
4. Connect to the virtual machines:
   - To connect to the Jenkins server: `vagrant ssh JenkinsServer`
   - To connect to the Nexus server: `vagrant ssh NexusServer`
   - To connect to the SonarQube server: `vagrant ssh SonarServer`

Once the virtual machines are up and running, you can access the Jenkins web interface by opening a browser and navigating to http://192.168.56.11:8080. Similarly, you can access the Nexus web interface by going to http://192.168.56.21:8081 and the SonarQube web interface by going to http://192.168.56.31.

## Jenkins Pipeline

The pipeline script Jenkinsfile defines a Jenkins pipeline that runs a series of stages to build, test, analyze, and deploy the Java web application. The pipeline uses Maven as the build tool and SonarQube for static code analysis. The pipeline script also uploads the built artifacts to a Nexus repository for artifact management.

The pipeline is structured into stages, each with a set of steps to execute. The stages are as follows:

1. Build: This stage builds the Java web application using Maven.
2. Test: This stage runs the unit tests using Maven.
3. Checkstyle Analysis: This stage runs the Checkstyle static code analysis tool using Maven.
4. Sonar Analysis: This stage runs the SonarQube code analysis tool using a SonarQube scanner.
5. Quality Gate: This stage waits for the SonarQube analysis to complete and checks the quality gate status. If the quality gate fails, the pipeline is aborted.
6. Upload Artifact: This stage uploads the built artifact (a WAR file) to Nexus repository for artifact management.
7. Slack Notification: This stage sends a notification to a specified Slack channel about the result of the pipeline.


To use the pipeline script, you must have a Jenkins instance set up and configured with the necessary plugins (Jenkins plugin, Maven Integration, GitHub Integration, Nexus Artifact Uploader, SonarQube Scanner, Slack Notification and Build Timestamp) and tools. 

To use this pipeline, you need to set up a Jenkins job that points to this repository and uses the Jenkinsfile as the pipeline definition. You also need to configure the following environment variables:

MAVEN3: The name of the Maven 3 tool installed in your Jenkins instance.
OracleJDK8: The name of the Oracle JDK 8 tool installed in your Jenkins instance.
vprofile-snapshot: The name of the Nexus snapshot repository to use.
vprofile-release: The name of the Nexus release repository to use.
vpro-maven-central: The name of the Nexus proxy repository for Maven Central.
nexuslogin: The name of a Jenkins credential that contains the Nexus username and password.
admin: The Nexus admin username.
admin: The Nexus admin password.
192.168.56.21: The IP address of the Nexus server.
8081: The port number of the Nexus server.
vpro-maven-group: The name of the Nexus group repository that includes the snapshot, release, and proxy repositories.
sonarserver: The name of the SonarQube server configured in your Jenkins instance.
sonarscanner: The name of the SonarQube scanner tool installed in your Jenkins instance.
Note that this pipeline assumes that Java web application is located in a directory called 'src/' at the root of your repository.

Once you have set up the Jenkins job and configured the environment variables, you can run the pipeline by clicking the "Build Now" button in the Jenkins web interface. The pipeline will run all the stages sequentially, and if everything is successful, it will deploy the built artifact to Nexus.

You can customize this pipeline to suit your specific needs by modifying the Jenkinsfile. For example, you can add additional stages, change the order of the stages, or modify the configuration of the analysis tools.

## License

This project is licensed under the [MIT license](LICENSE).
