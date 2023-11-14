pipeline {
    agent any

    environment {
        SONARQUBE_HOME = tool 'SonarQube-Scanner'
        MAVEN_HOME = tool 'Maven'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build and SonarQube Analysis') {
            steps {
                script {
                    def mavenHome = tool 'Maven'
                    def scannerHome = tool 'SonarQube-Scanner'

                    withEnv(["PATH+MAVEN=${mavenHome}/bin", "PATH+SONARQUBE_SCANNER=${scannerHome}/bin"]) {
                        sh 'mvn clean install sonar:sonar'
                    }
                }
            }
        }
    }

    post {
        always {
            // Cleanup or additional steps that should run regardless of success or failure
        }
    }
}