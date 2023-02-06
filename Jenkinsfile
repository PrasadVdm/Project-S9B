pipeline {
    agent any

    tools {
        jdk "OracleJDK8"
        maven "MAVEN3"
    }

    environment {
        NEXUSIP = '172.31.26.23'
        NEXUSPORT = '8081'
        NEXUS-GRP-REPO = 's9b-maven-grouped'
        NEXUS-USER = 'admin'
        NEXUS-PASS = 'admin'
        SNAP-REPO = 's9b-maven-snapshot'
        RELEASE-REPO = 's9b-release'
        CENTRAL-REPO = 's9b-maven-central'
        NEXUS-LOGIN = 'nexuslogin'
    }

    stages{
        stage('Build Code'){
            steps{
                sh "mvn -s settings.xml -DskipTests install"
            }
        }


    }


}