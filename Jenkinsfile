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
            nexusUrl: '3.106.164.181:8081', 
            nexusVersion: 'nexus3', 
            protocol: 'http', 
            repository: 'onlinebookstores', 
            version: '0.0.1-SNAPSHOT'
    }
        }
        stage('Copy to Tomcat Webapps') {
            steps {
                deploy adapters: [tomcat9(credentialsId: 'Tomcat', path: '', url: 'http://3.104.119.1:8090/')], contextPath: null, war: '**/*.war'
		}
	}
    }
}
