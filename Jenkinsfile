pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'arvind005/java-app'
        DOCKER_TAG = "jv${BUILD_NUMBER}"  // Dynamic tag with prefix 'jv'
        GIT_REPO = 'https://github.com/your-username/your-repo.git'
        HELM_CHART_PATH = 'helm/Java_Application'
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                script {
                    sh """
                    docker buildx build --platform linux/amd64,linux/arm64 \
                        -t ${DOCKER_IMAGE}:${DOCKER_TAG} --push .
                    """
                }
            }
        }

        stage('Update Helm Chart') {
            steps {
                script {
                    // Updated sed command to target the image tag precisely
                    sh """
                    sed -i '/image:/,/^[^ ]/ s|tag:.*|tag: ${DOCKER_TAG}|' ${HELM_CHART_PATH}/values.yaml
                    """
                    // Commit and push the updated Helm chart
                    sh """
                    git config --global user.name 'Arvindkarwal'
                    git config --global user.email 'arvindkarwal002@gmail.com'
                    git add ${HELM_CHART_PATH}/values.yaml
                    git commit -m 'Update Docker image tag to jv${DOCKER_TAG}'
                    git push origin main
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
