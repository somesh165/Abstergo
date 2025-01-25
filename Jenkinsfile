pipeline {
    agent any
    environment {
        //be sure to replace "bhavukm" with your own Docker Hub username
        DOCKER_IMAGE_NAME = "somesh165/train-schedule"
    }
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t somesh165/train-schedule:latest .'
                }
            }
        }
        
        stage('Deploy Kubernetes') {
            steps {
                script{
                    sh 'az login --service-principle -u 24493cd5-c8f5-4734-b70d-dfa046b6fa5f -p R3V8Q~pF_gg-qkcvw~MKK8ReHH6HUgYyW6mtgdar --tenant f22553fb-733c-4de3-8b6e-80a3e9b7ff04'
                    sh 'az account set --subscription 9d33cf9f-a70c-4c8d-b221-67ad92d8fa01'
                    sh 'az aks get-credentials --resource-group rg-aks --name devopsarch-1 --overwrite-existing'
                    sh 'kubectl apply -f train-schedule-kube.yml --validate=false'                
                }
            }
        }
        stage('DeployToProduction') {
            environment { 
                CANARY_REPLICAS = 0
            }
            steps {
                input 'Deploy to Production?'
                milestone(1)
                kubernetesDeploy(
                    kubeconfigId: 'kubeconfig',
                    configs: 'train-schedule-kube-canary.yml',
                    enableConfigSubstitution: true
                )
                kubernetesDeploy(
                    kubeconfigId: 'kubeconfig',
                    configs: 'train-schedule-kube.yml',
                    enableConfigSubstitution: true
                )
            }
        }
    }
}
