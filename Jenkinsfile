pipeline {
    agent any
    tools {
        maven 'M2_HOME'
    }
    environment {
        registry = '358966077154.dkr.ecr.us-east-1.amazonaws.com/geo_ecr_helm_repo'
        //registryCredential = 'ecr:us-east-1:awscreds'
        //dockerimage = ''
    }
    stages {
        stage('Code Checkout'){
            steps{
                git branch: 'main', url: 'https://github.com/WillKay28/geolocation_helm.git'
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
                    dockerImage.tag("$BUILD_NUMBER")
                }
            }
        }
        stage('Push Image'){
            steps{
                script{
                    sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 358966077154.dkr.ecr.us-east-1.amazonaws.com'
                    sh 'docker push 358966077154.dkr.ecr.us-east-1.amazonaws.com/geo_ecr_helm_repo:$BUILD_NUMBER'
                }
            }
        }
        stage('Helm Deploy 2 K8s'){
            steps {
                sh 'helm upgrade geo-first-release --install geo_helm_chart --namespace geo-helm-deploy --set image.tag=$BUILD_NUMBER'
            }
        }
    }
}
