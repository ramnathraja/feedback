pipeline {
    agent any

    environment {
        KUBECONFIG = '/var/lib/jenkins/kubeconfig'
        AWS_DEFAULT_REGION = 'ap-southeast-1' // Update with your desired region
        EKS_CLUSTER_NAME = 'your-eks-cluster' // Name of your EKS cluster
        //KUBE_CONFIG = credentials('kubeconfig') // Jenkins credential for kubeconfig file
        DOCKER_IMAGE = 'ramnathraja/feedback:latest' // Docker image from Docker Hub
        KUBE_NAMESPACE = 'default' // Namespace in EKS cluster
        DEPLOYMENT_NAME = 'demo-deployment' // Name of the deployment
        registry = "ramnathraja/feedback"
        registryCredential = 'docker-hub-credentials'
        dockerImage = ''
    }

    stages {
        stage('Git Checkout') {
            steps {
                script {
                    // Checkout the repository
                    git url: 'https://github.com/ramnathraja/feedback.git', branch: 'main'
                }
            }
        }
        stage('Building Docker Image') {
            steps {
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        stage('Push Image To Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
                        dockerImage.push("${env.BUILD_NUMBER}")
                        dockerImage.push("latest")
                    }
                }
            }
        }
    }

    //post {
        //always {
            //cleanWs() // Clean up workspace after the build
        //}
    //}
}
