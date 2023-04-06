// Define a color map for Slack notifications based on the result of the current build
def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE': 'danger',
]

pipeline {
    // Define the agent for the pipeline to run on any available agent
    agent any
    // Define the tools required for the pipeline to run, including Maven 3 and Oracle JDK 8
    tools {
        maven "MAVEN3"
        jdk "OracleJDK8"
    }

    // Define environment variables used throughout the pipeline
    environment {
        SNAP_REPO = 'vprofile-snapshot'
        NEXUS_USER = 'admin'
        NEXUS_PASS = 'admin'
        RELEASE_REPO = 'vprofile-release'
        CENTRAL_REPO = 'vpro-maven-central'
        NEXUSIP = '192.168.56.21'
        NEXUSPORT = '8081'
        NEXUS_GRP_REPO = 'vpro-maven-group'
        NEXUS_LOGIN = 'nexuslogin'
        SONARSERVER = 'sonarserver'
        SONARSCANNER = 'sonarscanner'
    }

    // Define the stages of the pipeline
    stages {
        // Build stage compiles the code and creates a WAR file
        stage('Build') {
            steps {
                sh 'mvn -s settings.xml -DskipTests install'   
            }
            
            // If the build is successful, archive the WAR file
            post {
                success {
                    echo "Now Archiving."
                    archiveArtifacts artifacts: '**/*.war'
                }
            } 
        }

        // Test stage runs the unit tests
        stage('Test'){
            steps {
                sh 'mvn -s settings.xml test'
            }
        }

        // Checkstyle Analysis stage runs Checkstyle to analyze the code
        stage('Checkstyle Analysis'){
            steps {
                sh 'mvn -s settings.xml checkstyle:checkstyle'
            }
        }

        // Sonar Analysis stage performs a static code analysis using SonarQube
        stage('Sonar Analysis') {
            environment {
                scannerHome = tool "${SONARSCANNER}"
            }
            steps {
                withSonarQubeEnv("${SONARSERVER}") {
                    sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                    -Dsonar.projectName=vprofile \
                    -Dsonar.projectVersion=1.0 \
                    -Dsonar.sources=src/ \
                    -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                    -Dsonar.junit.reportsPath=target/surefire-reports/ \
                    -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                    -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
                }
            }
        }

        // Quality Gate stage waits for the SonarQube analysis to complete and checks whether the quality of the code meets the specified requirements
        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                waitForQualityGate abortPipeline: true
                }
            }
        }

        // UploadArtifact stage uploads the WAR file to the Nexus repository
        stage("UploadArtifact"){
            steps{
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: "${NEXUSIP}:${NEXUSPORT}",
                    groupId: 'QA',
                    version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                    repository: "${RELEASE_REPO}",
                    credentialsId: "${NEXUS_LOGIN}",
                    artifacts: [
                        [artifactId: 'vproapp',
                            classifier: '',
                            file: 'target/vprofile-v2.war',
                            type: 'war']
                    ]
                )
            }
        }
    }

    post {
        always {
            // Send Slack notification with the build result and a link to the build URL
            echo 'Slack Notifications.'
            slackSend channel: '#jenkinscicdlocal',
                color: COLOR_MAP[currentBuild.currentResult], // Set the message color based on the build result (SUCCESS = green, FAILURE = red)
                message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
        }
    }
}

