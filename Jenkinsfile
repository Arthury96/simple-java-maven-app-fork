pipeline {
    agent any

    environment {
        SONARQUBE_HOME = tool 'SonarQube-Scanner' // need to install in jenkins
        MAVEN_HOME = tool 'Maven' // need ti instal in jenkins
        
        // NEXUS_CREDENTIAL_ID = "nexusCredential"
        // NEXUS_SERVER = 'http://172.18.0.3:8081'

        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "172.18.0.3:8081"
        NEXUS_REPOSITORY = "LoginWebApp"
        NEXUS_CREDENTIAL_ID = "nexusCredential"


        
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

        // stage('Deploy to Nexus') {
        //     steps {
        //         script {
        //             sh "${MAVEN_HOME}/bin/mvn clean install"
        //             // Deploy artifacts to Nexus using Maven
        //             sh "${MAVEN_HOME}/bin/mvn deploy -Dmaven.test.skip=true -DaltDeploymentRepository=nexus::default::${NEXUS_SERVER}/repository/LoginWebApp/"
        //         }
        //     }
        // }

                stage("Publish to Nexus Repository Manager") {
            steps {
                script {
                    pom = readMavenPom file: "pom.xml";
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    artifactPath = filesByGlob[0].path;
                    artifactExists = fileExists artifactPath;
                    if(artifactExists) {
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                        nexusArtifactUploader(
                            nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
                            groupId: pom.groupId,
                            version: pom.version,
                            repository: NEXUS_REPOSITORY,
                            credentialsId: NEXUS_CREDENTIAL_ID,
                            artifacts: [
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: artifactPath,
                                type: pom.packaging],
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: "pom.xml",
                                type: "pom"]
                            ]
                        );
                    } else {
                        error "*** File: ${artifactPath}, could not be found";
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
