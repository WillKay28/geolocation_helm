pipeline {
    agent any
    tools {
        maven 'M2_HOME'
    }
    environment {
        registry = '358966077154.dkr.ecr.us-east-1.amazonaws.com/geolocation_ecr_rep'
        //registryCredential = 'ecr:us-east-1:awscreds'
        dockerimage = ''
    }
    stages {
        stage('Code Checkout'){
            steps{
                git branch: 'main', url: 'https://github.com/WillKay28/geolocation.git'
            }
        }
        stage('Code Build'){
            steps{
                sh 'mvn clean package'
            }
        }
        stage('Code Test'){
            steps{
                sh 'mvn test'
            }
        }
        stage('Image Build'){
            steps{
                script{
                    dockerImage = docker.build registry
                }
            }
        }
        stage('Push Image'){
            steps{
                script{
                    sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 358966077154.dkr.ecr.us-east-1.amazonaws.com'
                    sh 'docker push 358966077154.dkr.ecr.us-east-1.amazonaws.com/geolocation_ecr_rep:latest'
                }
            }
        }
        stage('K8s Deploy'){
            steps {
                withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'eks_credential', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
                    sh "kubectl apply -f eks_deploy_from_ecr.yaml"
                }
            }
        }
    }
}
//end