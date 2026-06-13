pipeline {
    agent any
    
    tools {
        // These names must match exactly what you named them in Step 2
        jdk 'java17' 
        maven 'maven3'
    }

    stages {
        stage('Checkout') {
            steps {
                // Clones your forked repository
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
                // Tells Maven to run tests and continue even if some fail 
                // so Jenkins can capture the test reports
                sh 'mvn test'
            }
        }

        stage('Package') {
            steps {
                echo 'Packaging application into a JAR...'
                // Skips tests here since we ran them in the previous stage
                sh 'mvn package -DskipTests'
            }
        }
    }

    post {
        always {
            echo 'Archiving test results...'
            // Captures JUnit test results from the target folder
            junit '**/target/surefire-reports/*.xml'
        }
        success {
            echo 'Archiving build artifacts...'
            // Saves the generated JAR file so you can download it from Jenkins
            archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
        }
    }
}
