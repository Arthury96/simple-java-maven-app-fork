pipeline {

    agent {
        docker {
            image 'maven:3.9.5-eclipse-temurin-17-alpine'
            args '-v /root/.m2:/root/.m2'
        }
        label 'linux'
    }
    
    options {
        skipStagesAfterUnstable()
        buildDiscarder(logRotator(numToKeepStr: '5'))
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }

    stage('Scan') {
        steps {
            withSonarQubeEnv(installationName: 'sq1') { 
            sh './mvnw clean org.sonarsource.scanner.maven:sonar-maven-plugin:3.9.0.2155:sonar'
                }
            }
        }


        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Deliver') { 
            steps {
                sh './scripts/deliver.sh' 
            }
        }
    }
}