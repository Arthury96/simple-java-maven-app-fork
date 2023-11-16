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
                    def server = Artifactory.server NEXUS_SERVER
                    def credentials = Jenkins.instance.get('nexusCredential')

                    server.publishBuildInfo buildInfo: 'buildinfo.json'

                    server.upload spec: '''{
                        "files": [
                            {
                                "pattern": "target/*.jar",
                                "target": "your/nexus/repository/path/"
                            }
                        ]
                    }''', buildInfo: 'buildinfo.json'
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
