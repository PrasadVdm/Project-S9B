def COLOR_MAP = [
    'SUCCESS': 'good',
    'FAILURE': 'danger',
]
pipeline {
    agent any

    tools {
        jdk "OracleJDK8"
        maven "MAVEN3"
    }

    environment{
        NEXUSIP = "172.31.26.23"
        NEXUSPORT = "8081"
        NEXUS_USER = "admin"
        NEXUS_PASS = "admin"
        SNAP_REPO = "s9b-maven-snapshot"
        RELEASE_REPO = "s9b-release"
        CENTRAL_REPO = "s9b-maven-central"
        NEXUS_GRP_REPO = "s9b-maven-grouped"
        NEXUS_LOGIN = "nexuslogin"
        SONARSCANNER = "sonar-s9b"
        SONARSERVER = "sonar-server-s9b"
    }

    stages{
        stage('Build Code'){
            steps{
                sh "mvn -s settings.xml -DskipTests install"
            }
            post{
                success{
                    echo "Archiving artifact..."
                    archiveArtifacts artifact = "**/*.war"
                }
            }
        }

        stage('Unit Test'){
            steps{
                sh 'mvn -s settings.xml test'
            }
        }

        stage('Checkstyle analysis'){
            steps{
                sh 'mvn -s settings.xml checkstyle:checkstyle'
            }
            post{
                success{
                    echo "Genereated Checkstyle analysis report"
                }
            }
        }

        stage('SonarQube analysis') {
            environment {
                scannerHome = tool "${SONARSCANNER}" // the name you have given the Sonar Scanner (in Global Tool Configuration)
            }
            steps {
                withSonarQubeEnv(installationName: "${SONARSERVER}") {
                    sh '''${scannerHome}/bin/sonar-scanner -X \
                    -Dsonar.projectKey=testingS9B \
                    -Dsonar.projectName=projects9b \
                    -Dsonar.projectVersion=2.0 \
                    -Dsonar.sources=src/ \
                    -Dsonar.junit.reportsPath=target/surefire-reports/ \
                    -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                    -Dsonar.java.checkstyle.reportsPath=target/checkstyle-result.xml \
                    -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/'''
                }
            }

        }

        stage("Quality Gate") {
            steps {
              timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
              }
            }
        }

        stage("Uploading Artifact to Nexus") {
            steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: "${NEXUSIP}:${NEXUSPORT}",
                    groupId: 'QA',
                    version: "V${BUILD_ID}-${BUILD_TIMESTAMP}",
                    repository: "${RELEASE_REPO}",
                    credentialsId: "${NEXUS_LOGIN}",
                    artifacts: [
                        [artifactId: 'S9B',
                        classifier: '',
                        file: 'target/vprofile-v2.war',
                        type: 'war']
                    ]
                )
            }
        }

    }
    post {
        always {
            echo 'Slack Notifications..'
            slackSend channel: '#s9bcicd',
            color: COLOR_MAP[currentBuild.currentResult],
            message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
            
        }
    }


}
###