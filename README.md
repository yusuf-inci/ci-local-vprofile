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

This file is a Jenkins pipeline script that defines a CI pipeline. The pipeline consists of several stages, including Build, Test, Checkstyle Analysis, Sonar Analysis, Quality Gate, and UploadArtifact.

The pipeline is configured to run on any available agent and to use Maven 3 and Oracle JDK 8. It also defines several environment variables, including the Nexus repository name and credentials, the SonarQube server and scanner, and the color map for Slack notifications.

The Build stage compiles the code and creates a WAR file. If the Build stage is successful, the post section archives the WAR file.

The Test stage runs the unit tests.

The Checkstyle Analysis stage runs Checkstyle to analyze the code.

The Sonar Analysis stage performs a static code analysis using SonarQube. It sets the environment variable for the SonarQube scanner and runs the scanner with specific configurations.

The Quality Gate stage waits for the SonarQube analysis to complete and checks whether the quality of the code meets the specified requirements. If the quality is not met, the pipeline will abort.

The UploadArtifact stage uploads the WAR file to the Nexus repository.

Finally, the post section sends a Slack notification with the status of the pipeline using the defined color map. If the pipeline fails, the Slack message will be colored red, and if it succeeds, it will be colored green.

To use the pipeline script, you must have a Jenkins instance set up and configured with the necessary plugins (Jenkins plugin, Maven Integration, GitHub Integration, Nexus Artifact Uploader, SonarQube Scanner, Slack Notification and Build Timestamp) and tools. 

To use this pipeline, you need to set up a Jenkins job that points to this repository and uses the Jenkinsfile as the pipeline definition. You also need to configure the environment variables.

Once you have set up the Jenkins job and configured the environment variables, you can run the pipeline by clicking the "Build Now" button in the Jenkins web interface. The pipeline will run all the stages sequentially, and if everything is successful, it will deploy the built artifact to Nexus.

You can customize this pipeline to suit your specific needs by modifying the Jenkinsfile. For example, you can add additional stages, change the order of the stages, or modify the configuration of the analysis tools.

## License

This project is licensed under the [MIT license](LICENSE).
