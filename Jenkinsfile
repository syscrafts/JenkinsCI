pipeline{
    agent any
    
    environment{
        DOCKERHUB_USERNAME = "samratchaudhary"
        APP_NAME = "jenkinsci_argocd"
        IMAGE_TAG = "${BUILD_NUMBER}"
        IMAGE_NAME = "${DOCKERHUB_USERNAME}"+"/"+"${APP_NAME}"
        REGISTRY_CREDS = 'dockerhub'
    }

    stages{
        stage('cleanup workspace'){
            steps{
                script{
                    cleanWs()
                }
            }
        }
        stage('Checkout SCM'){
            steps{
                script{
                    git credentialsId: 'github',
                    url: 'https://github.com/syscrafts/ArgoCD.git',
                    branch: 'main'
                }
            }
        }
        stage('Build Docker Image'){
            steps{
                script{
                    #docker_image = docker.build "${IMAGE_NAME}" 
                    docker_image = docker.build("samratchaudhary/jenkinsci") 
                }
            }
        }
        stage('Push Docker Image'){
            steps{
                script{
                    docker.withRegistry('', REGISTRY_CREDS){
                        docker_image.push("$BUILD_NUMBER")
                        docker_image.push('latest')
                    }
                }
            }
        }
        stage('Delete Docker Image'){
            steps{
                script{
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker rmi ${IMAGE_NAME}:latest"

                }
            }
        }
        stage('Trigger config change pipeline'){
            steps{
                script{
                    sh "curl -v -k --user samrat:11706da0dbdabc79169daadbf1777a84fa -X POST -H 'cache-control: no-cache' -H 'content-type: application/x-www-form-urlencoded' --data 'IMAGE_TAG=${IMAGE_TAG}' 'http://35.172.190.161:8080/job/gitops_argo_CD/buildWithParametes?token=config'"
                }
            }
        }
    }
}

