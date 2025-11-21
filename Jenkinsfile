pipeline {
    agent any

    environment {
        IMAGE_NAME = "aravind310730/devops-restart"
        TAG = "latest"
        DOCKERHUB_CRED = credentials('dockerhub_cred') // Docker Hub credentials
        DEPLOYMENT_NAME = "restart-app"
        CONTAINER_NAME = "restart"
        NAMESPACE = "default"
        KIND_CLUSTER_NAME = "devops"
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

        stage('Trivy Scan') {
            steps {
                sh "trivy image ${IMAGE_NAME}:${TAG} || true"
            }
        }

        stage('Login to Docker Hub') {
            steps {
                sh """
                echo "${DOCKERHUB_CRED_PSW}" | docker login -u "${DOCKERHUB_CRED_USR}" --password-stdin
                """
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh "docker push ${IMAGE_NAME}:${TAG}"
            }
        }

        stage('Load Image into Kind') {
            steps {
                sh "kind load docker-image ${IMAGE_NAME}:${TAG} --name ${KIND_CLUSTER_NAME}"
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                // Use Jenkins secret file for kubeconfig
                withCredentials([file(credentialsId: 'kubeconfig_id', variable: 'KUBECONFIG')]) {
                    sh """
                    kubectl --kubeconfig=$KUBECONFIG set image deployment/${DEPLOYMENT_NAME} \
                    ${CONTAINER_NAME}=${IMAGE_NAME}:${TAG} -n ${NAMESPACE}
                    kubectl --kubeconfig=$KUBECONFIG rollout status deployment/${DEPLOYMENT_NAME} -n ${NAMESPACE}
                    """
                }
            }
        }
    }

    post {
        always {
            sh "docker logout || true"
        }
        success {
            echo "Pipeline completed and deployment updated successfully!"
        }
        failure {
            echo "Pipeline failed! Check the logs."
        }
    }
}




