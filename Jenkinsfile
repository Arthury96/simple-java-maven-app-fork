pipeline {
    agent any

    environment {
        SONARQUBE_HOME = tool 'SonarQube-Scanner' // need to install in jenkins
        MAVEN_HOME = tool 'Maven' // need ti instal in jenkins
        
        NEXUS_CREDENTIAL_ID = "nexusCredential"
        NEXUS_SERVER = 'http://172.18.0.3:8081'


        
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('SonarQube Analysis') {
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

        stage('Deploy to Nexus') {
            steps {
                script {
                    // Deploy artifacts to Nexus using Maven
                    sh "${MAVEN_HOME}/bin/mvn deploy -Dmaven.test.skip=true -DaltDeploymentRepository=nexus::default::${NEXUS_SERVER}/repository/LoginWebApp/"
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
