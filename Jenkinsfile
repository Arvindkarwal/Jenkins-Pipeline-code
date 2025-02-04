pipeline {
    agent any

    environment {
        // Define environment variables
        DOCKER_IMAGE = 'arvind005/java-app'
        DOCKER_TAG = 'v02'
        GIT_REPO = 'https://github.com/your-username/your-repo.git'
        HELM_CHART_PATH = 'path/to/helm/chart' // Path to your Helm chart in the repo
    }

    stages {
        // Stage 1: Build the code using Maven
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        // Stage 2: Run JUnit tests
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        // Stage 3: Build and push Docker image
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

        // Stage 4: Update Helm chart with the new Docker image tag
        stage('Update Helm Chart') {
            steps {
                script {
                    // Update the Helm values.yaml file with the new Docker image tag
                    sh """
                    sed -i 's|tag:.*|tag: ${DOCKER_TAG}|g' ${HELM_CHART_PATH}/values.yaml
                    """
                    // Commit and push the updated Helm chart
                    sh """
                    git config --global user.name 'Jenkins'
                    git config --global user.email 'jenkins@example.com'
                    git add ${HELM_CHART_PATH}/values.yaml
                    git commit -m 'Update Docker image tag to ${DOCKER_TAG}'
                    git push origin main
                    """
                }
            }
        }
    }

    // Post-build actions
    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}