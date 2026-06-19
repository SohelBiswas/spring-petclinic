pipeline {
    agent any
    
    tools {
        maven 'maven3' 
    }

    environment {
        // 1. REPLACE THIS with the token you generated in SonarQube
        SONAR_TOKEN = 'squ_4fbc7f1ec72159d5175ee3efe9f28ba7a212124d'
        // This targets your Mac's localhost from inside the Jenkins container
        SONAR_HOST_URL = 'http://host.docker.internal:9000'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Clean & Compile') {
            steps {
                echo 'Compiling the application...'
                sh 'mvn clean compile'
            }
        }

        stage('Run Tests') {
            steps {
                echo 'Running unit tests...'
                sh 'mvn test'
            }
        }

        stage('Trivy File System Scan') {
            steps {
                echo 'Scanning code directory for vulnerabilities using Trivy...'
                // Launches a lightweight Trivy container to scan the workspace filesystem
                sh "docker run --rm -v /var/run/docker.sock:/var/run/docker.sock -v \$(pwd):/apps aquasec/trivy:latest fs /apps"
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo 'Injecting code analysis into SonarQube...'
                // Triggers the Maven Sonar plugin using the environment variables set above
                sh "mvn sonar:sonar -Dsonar.token=${SONAR_TOKEN} -Dsonar.host.url=${SONAR_HOST_URL} -Dsonar.projectKey=spring-petclinic"
            }
        }

        stage('Package') {
            steps {
                echo 'Packaging application...'
                sh 'mvn package -DskipTests'
            }
        }
    }

    post {
        always {
            echo 'Archiving test results...'
            junit '**/target/surefire-reports/*.xml'
        }
    }
}
