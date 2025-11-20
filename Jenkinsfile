pipeline {
    agent any

    environment {

        dockerhub_cred = credentials('dockerhub_cred')
        IMAGE_NAME = "aravind310730/devops-restart"
        TAG = "latest"
    }

    stages {

        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/aravind30122003/devops-restart.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${TAG} ."
            }
        }
        stage('trivy scan') {
            steps {
               script{
                sh "trivy image --exit-code 2 --severity CRITICAL<HIGH ${IMAGE_NAME}:${TAG} ."
            }
          }
       }

        stage('Login to Docker Hub') {
            steps {
                sh """
                echo "${dockerhub_cred_PSW}" | docker login -u "${dockerhub_cred_USR}" --password-stdin
                """
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh "docker push ${IMAGE_NAME}:${TAG}"
            }
        }
    }

    post {
        always {
            sh "docker logout || true"
        }
    }
}

