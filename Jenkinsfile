pipeline {
    agent any

    environment {
        registry = "feedback/cicd-k8s"
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

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Add your Kubernetes deployment commands here
                    sh 'kubectl apply -f deployment.yml'
                }
            }
        }
    }

    post {
        always {
            cleanWs()  // Clean up workspace after the build
        }
    }
}
