pipeline {
    agent any

    environment {
        DOCKERHUB_USER = "aravind310730" // store only username in Jenkins
        dockerhub_cred = credentials('dockerhub_cred') // store password/token here
        IMAGE_NAME = "aravind310730/devops-restart"   // change to your repo name
        TAG = "latest"
    }

    stages {

        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/aravind30122003/devops-restart/repo.git'
           }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                docker build -t ${IMAGE_NAME}:${TAG} .
                """
            }
        }

        stage('Login to Docker Hub') {
            steps {
                sh """
                echo "${dockerhub_cred}" | docker login -u "${DOCKERHUB_USER}" --password-stdin
                """
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh """
                docker push ${IMAGE_NAME}:${TAG}
                """
            }
        }

    }

    post {
    always {
        node {
            sh "docker logout || true"
        }
    }
}

}

