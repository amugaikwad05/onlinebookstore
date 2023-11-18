pipeline {
    agent any

    tools {
        // Name should match the one you configured in the Global Tool Configuration
        maven 'maven'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install'
        }
    }
        stage('Publish to Nexus') {
    steps {
        nexusArtifactUploader artifacts: [
         [
             artifactId: 'onlinebookstore', 
             classifier: '', 
             file: '/var/lib/jenkins/workspace/onlinebookstores/target/onlinebookstore-0.0.1-SNAPSHOT.war', 
             type: 'war'
                    ]
        ], 
            credentialsId: 'nexus', 
            groupId: 'onlinebookstore', 
            nexusUrl: '13.52.183.90:8081', 
            nexusVersion: 'nexus3', 
            protocol: 'http', 
            repository: 'onlinebookstore', 
            version: '0.0.1-SNAPSHOT'
    }
        }

        stage('Deploy to Tomcat') {
            steps {
                script {
                    def tomcatUrl = 'http://54.67.7.193:8090/'  // Replace with your Tomcat URL
                    def tomcatManagerCredentialsId = 'Tomcat'  // Jenkins credentials for Tomcat Manager
                    def warFileName = '/var/lib/jenkins/workspace/onlinebookstores/target/onlinebookstore-0.0.1-SNAPSHOT.war'  // The path to your WAR file

                    if (fileExists(warFileName)) {
                       sh "curl --user tomcat-user:tomcat-password --upload-file ${warFileName} ${tomcatUrl}/manager/text/deploy?path=/onlinebookstore&update=true"
                    } else {
                        error("WAR file not found")
                    }
                }
            }
        }
        stage('Copy to Tomcat Webapps') {
            steps {
                script {
                    def warFileName = '/var/lib/jenkins/workspace/onlinebookstores/target/onlinebookstore-0.0.1-SNAPSHOT.war'  // The path to your WAR file
                    def tomcatWebappsDirectory = '/opt/apache-tomcat-10.1.15/webapps'

                    if (fileExists(warFileName)) {
                        sh "cp -r ${warFileName} ${tomcatWebappsDirectory}"
                    } else {
                        error("WAR file not found for copying to Tomcat webapps")
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment to Tomcat successful'
        }
        failure {
            echo 'Deployment to Tomcat failed'
        }
    }
}

def fileExists(filePath) {
    try {
        sh "test -e ${filePath}"
        return true
    } catch (Exception ignored) {
        return false
    }
}
