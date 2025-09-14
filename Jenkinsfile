pipeline {
    agent any
    tools {
        maven 'maven'
    }
    environment {
        IMAGE_NAME = "springbootapp"
        IMAGE_TAG = "latest"
        ACR_NAME = "jenkinsdocker" 
        ACR_LOGIN_SERVER = "${ACR_NAME}.azurecr.io"
        FULL_IMAGE_NAME = "${ACR_LOGIN_SERVER}/${IMAGE_NAME}:${IMAGE_TAG}"
        TENANT_ID = "c52d77bf-a68c-4cdc-8c07-cd47ca3b978e"
        RESOURCE_GROUP = "demo-rg"
        CLUSTER_NAME    = "demo-eks"
    }
    stages {
        stage('Chekcout from Git') {
            steps {
                git branch: 'main', url: 'https://github.com/MVAL432/springboot-petclinic-jenkins.git'
            }
        }
    //     stage('Maven validate') {
    //         steps {
    //             sh 'mvn validate'
    //         }
    //     }
    //     stage('Maven compile') {
    //         steps {
    //             sh 'mvn compile'
    //         }
    //     }
    //    stage('SonarQube analysis') {
    //         environment {
    //             SCANNER_HOME = tool 'sonar-scanner'
    //         }
    //         steps {
    //             withSonarQubeEnv('sonarserver') {
    //                 sh  '''
    //                 $SCANNER_HOME/bin/sonar-scanner \
    //                 -Dsonar.organization=anand-devops \
    //                 -Dsonar.projectName=petclinic \
    //                 -Dsonar.projectKey=anand-devops_petclinic \
    //                 -Dsonar.java.binaries=.
    //                 '''
    //             }
    //         }
    //     }
        stage('Maven package') {
            steps {
                sh 'mvn package'
            }
        }
        // stage('Sonar Quality Gate') {
        //     steps {  
        //         timeout(time: 1, unit: 'MINUTES') {
        //             waitForQualityGate abortPipeline: true, credentialsId: 'sonar'
        //         }
        //     }
        // }
        stage('Docker Build') {
            steps {
                script {
                    echo 'Building Docker Image'
                    docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                }
            }
        }
        stage('Azure login to ACR') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'azure-acr-sp',usernameVariable: 'AZURE_USERNAME', passwordVariable: 'AZURE_PASSWORD')]) {
                  script {
                    echo "login to Azure"
                    sh '''
                    az login --service-principal -u $AZURE_USERNAME -p $AZURE_PASSWORD --tenant $TENANT_ID
                    az acr login --name $ACR_NAME
                    '''
                  }  
                }  
            }
        }
        stage('Docker Push to ACR') {
            steps {
                script {
                    echo "Docker image push"
                    sh '''
                    docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${FULL_IMAGE_NAME}
                    docker push ${FULL_IMAGE_NAME}
                    '''
                }
            }
        }
        stage('Login to AKS cluster') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'azure-acr-sp',usernameVariable: 'AZURE_USERNAME', passwordVariable: 'AZURE_PASSWORD')]) {
                  script {
                    echo "login to Azure"
                    sh '''
                    az login --service-principal -u $AZURE_USERNAME -p $AZURE_PASSWORD --tenant $TENANT_ID
                    az aks get-credentials --resource-group $RESOURCE_GROUP --name $CLUSTER_NAME --overwrte-existing
                    '''
                  }  
                }  
            }
        }
        stage('Deploy to AKS') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'azure-acr-sp',usernameVariable: 'AZURE_USERNAME', passwordVariable: 'AZURE_PASSWORD')]) {
                script {
                    echo "Deploying to AKS"
                    sh '''
                    kubectl apply -f k8s/sprinboot-deployment.yaml
                    '''
                    }
                }
            }
        }
    }
}