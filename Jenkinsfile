wpipeline {
    agent any

    environment {
        IMAGE_NAME = "aravind310730/devops-restart"
        TAG = "${env.BUILD_NUMBER}"           // version each build
        DOCKERHUB_CRED = credentials('dockerhub_cred')
        KUBE_CONFIG = "/var/jenkins_home/.kube/config"
        RELEASE = "restart-app"
        NAMESPACE = "default"
    }

    stages {

        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/aravind30122003/devops-restart.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${TAG} ."
            }
        }

        stage('Security Scan (Trivy)') {
            steps {
                sh """
                trivy image --severity HIGH,CRITICAL --exit-code 1 ${IMAGE_NAME}:${TAG} || true
                """
            }
        }

        stage('Login to Docker Hub') {
            steps {
                sh """
                echo "${DOCKERHUB_CRED_PSW}" | docker login -u "${DOCKERHUB_CRED_USR}" --password-stdin
                """
            }
        }

        stage('Push Image') {
            steps {
                sh "docker push ${IMAGE_NAME}:${TAG}"
            }
        }

        stage('Deploy with Helm') {
            steps {
                sh """
                helm upgrade --install ${RELEASE} ./helm/ \
                --namespace ${NAMESPACE} --create-namespace \
                --set image.repository=${IMAGE_NAME} \
                --set image.tag=${TAG}
                """
            }
        }

        stage('Verify Rollout') {
            steps {
                sh """
                kubectl --kubeconfig=${KUBE_CONFIG} rollout status deployment/${RELEASE} -n ${NAMESPACE}
                """
            }
        }
    }

    post {
        success {
            echo "🚀 Deployment successful: version ${TAG}"
        }
        failure {
            echo "❌ Deployment failed. Check logs."
        }
        always {
            sh "docker logout || true"
        }
    }
}



