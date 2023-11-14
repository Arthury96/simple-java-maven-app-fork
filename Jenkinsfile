pipeline {
    agent any
    
    environment {
        SONARQUBE_HOME = tool 'SonarQube-Scanner'
        MAVEN_HOME = tool 'Maven'
    }
    
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // Run SonarQube analysis
                script {
                    withSonarQubeEnv('SonarQube-Server') {
                        sh "${MAVEN_HOME}/bin/mvn sonar:sonar"
                    }
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