pipeline {
    agent any
    tools {
        maven 'Maven' // Ensure this matches the name you set in the configuration
    }
    
    environment {
        // Replace 'your-sonarqube-server' with the name configured in Jenkins
        SONARQUBE_SERVER = 'Sonar'
        // Set your SonarQube project key
        SONAR_PROJECT_KEY = 'jenkins-sonaa'
        // SonarQube project name (optional)
        SONAR_PROJECT_NAME = 'jenkins-sonaa'
        // Authentication token from SonarQube
        SONAR_TOKEN = credentials('sonarqube') // Credential ID for SonarQube token in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code...'
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                script {
                    // SonarQube scanner for Jenkins is used to run the analysis
                    withSonarQubeEnv(Sonar) {
                        // Adjust command according to your project language and structure
                        sh """
                        sonar-scanner \
                          -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                          -Dsonar.projectName=${SONAR_PROJECT_NAME} \
                          -Dsonar.sources=src/main/java \
                          -Dsonar.login=${SONAR_TOKEN}
                        """
                    }
                }
            }
        }
        
        stage('Quality Gate') {
            steps {
                // Wait for SonarQube to process the results
                script {
                    timeout(time: 2, unit: 'MINUTES') {
                        waitForQualityGate abortPipeline: true
                    }
                }
            }
        }
    }
    
    post {
        always {
            echo 'Cleaning up...'
            cleanWs() // Clean workspace after job
        }
        success {
            echo 'Pipeline succeeded.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
