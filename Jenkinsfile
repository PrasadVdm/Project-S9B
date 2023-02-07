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


    }


}