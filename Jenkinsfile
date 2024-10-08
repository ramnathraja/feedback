pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = 'ap-southeast-1' // Update with your desired region
        EKS_CLUSTER_NAME = 'your-eks-cluster' // Name of your EKS cluster
        KUBE_CONFIG = credentials('kubeconfig') // Jenkins credential for kubeconfig file
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
        stage('EKS Cluster Operations') {
            steps {
                withAWS(credentials: 'aws-eks-credentials') {
                    // Execute AWS CLI commands or eksctl commands here
                    sh 'eksctl create cluster --name my-eks-cluster --region ap-southeast-1 --nodegroup-name my-nodes --nodes 2 --nodes-min 1 --nodes-max 2 --managed'
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Add your Kubernetes deployment commands here
                    // sh 'kubectl apply -f deployment.yml --validate=false'
                    // Apply the Kubernetes deployment with the new Docker image
                    sh '''
                    kubectl set image deployment/$DEPLOYMENT_NAME -n $KUBE_NAMESPACE $DEPLOYMENT_NAME=$DOCKER_IMAGE --kubeconfig $KUBE_CONFIG
                    kubectl rollout status deployment/$DEPLOYMENT_NAME -n $KUBE_NAMESPACE --kubeconfig $KUBE_CONFIG
                    '''
                }
            }
        }
    }

    post {
        always {
            cleanWs() // Clean up workspace after the build
        }
    }
}
