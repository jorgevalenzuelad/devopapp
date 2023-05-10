pipeline {
    agent any
        stages {
          stage('Build') {
            steps { 
                sh 'mvn -B package'
            }
        }
        stage('SonarQube analysis') {
             environment {
                SCANNER_HOME = tool 'SonarQube Conexion'
            }
            steps {
              withSonarQubeEnv(credentialsId: 'tockernuevoa', installationName: 'SonarInDocker') {
              sh '''$SCANNER_HOME/bin/sonar-scanner \
            -Dsonar.projectKey=devopapp \
            -Dsonar.projectName=devopapp \
            -Dsonar.sources=./ \
            -Dsonar.java.binaries=target/classes/ \
            -Dsonar.exclusions=src/test/java/****/*.java \
            -Dsonar.projectVersion=${BUILD_NUMBER}-${GIT_COMMIT_SHORT}'''
             }
            }
        }
        stage("Publish to Nexus Repository Manager") {
            steps {
                script {
                    def pom = readMavenPom file: "pom.xml";
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    artifactPath = filesByGlob[0].path;
                    artifactExists = fileExists artifactPath;
                    if(artifactExists) {
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                        nexusArtifactUploader(
                            nexusVersion: "nexus3",
                            protocol: "http",
                            nexusUrl: "34.69.44.55:8081",
                            groupId: pom.groupId,
                            version: pom.version,
                            repository: "repomvndevops",
                            credentialsId: "nexusgoo",
                            artifacts: [
                                [artifactId: pom.artifactId,
                                        classifier: '',
                                        file: artifactPath,
                                        type: pom.packaging]
                           ]
                        );
                    } else {
                        error "*** File: ${artifactPath}, could not be found";
                    }
                }
            }
            }
     }
    post{
        always{
            slackSend( channel: "#fundamentos-de-devops", token: "tokenslack", color: "good", message: "$CHANGE_AUTHOR")
        }
    }
}
   def custom_msg()
        {
        def JENKINS_URL= "34.69.44.55:8080"
        def JOB_NAME = env.JOB_NAME
        def BUILD_ID= env.BUILD_ID
        def JENKINS_LOG= " FAILED: Job [${env.JOB_NAME}] Logs path: ${JENKINS_URL}/job/${JOB_NAME}/${BUILD_ID}/consoleText"
        return 
}