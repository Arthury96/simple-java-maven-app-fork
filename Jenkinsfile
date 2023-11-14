pipeline {
    agent any

    environment {
        SONARQUBE_HOME = tool 'SonarQube-Scanner' // need to install in jenkins
        MAVEN_HOME = tool 'Maven' // need ti instal in jenkins
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

                 withSonarQubeEnv('sq1') {
                        withEnv(["PATH+MAVEN=${mavenHome}/bin", "PATH+SONARQUBE_SCANNER=${scannerHome}/bin"]) {
                            sh 'mvn clean install sonar:sonar'
                        }
                    }
                }
            }
        }
    }

    post {
        always {
            junit 'target/surefire-reports/*.xml'
        }
    }
}