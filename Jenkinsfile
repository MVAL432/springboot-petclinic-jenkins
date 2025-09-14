pipeline {
    agent any
    tools {
        maven 'maven'
    }

    stages {
        stage('Chekcout from Git') {
            steps {
                git branch: 'main', url: 'https://github.com/MVAL432/springboot-petclinic-jenkins.git'
            }
        }
        stage('Maven validate') {
            steps {
                sh 'mvn validate'
            }
        }
        stage('Maven compile') {
            steps {
                sh 'mvn compile'
            }
        }
    //    stage('SonarQube analysis') {
    //         environment {
    //             SCANNER_HOME = tool 'Soanr-Scanner'
    //         }
    //         steps {
    //             withSonarQubeEnv('sonarserver') {
    //                 sh  '''
    //                 $SCANNER_HOME/bin/sonar-scanner \
    //                 -Dsonar.organization=myorg \
    //                 -Dsonar.projectName=springboot-petclinic-jenkins \
    //                 -Dsonar.projectKey=springboot-petclinic-jenkins \
    //                 -Dsonar.java.binaries=.
    //                 '''
    //             }
    //         }
    //     }
    //     stage('Maven package') {
    //         steps {
    //             sh 'mvn package'
    //         }
    //     }
    //     stage('Sonar Quality Gate') {
    //         steps {  
    //             timeout(time: 1, unit: 'MINUTES') {
    //                 waitForQualityGate abortPipeline: true, credentialsId: 'sonar'
    //             }
    //         }
    //     }
    //     stage('Docker Build') {
    //         steps {
    //             script {
    //                 echo 'Building Docker Image'
    //                 docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
    //             }
    //         }
    //     }
    //     stage('Azure login to ACR') {
    //         steps{
    //         withCredentials()}
    //     }



    }
}