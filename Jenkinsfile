pipeline {

    agent any

    environment {
        DOCKER_REGISTRY = "raguyazhin"
        DOCKER_IMAGE_NAME = "node-backend-app"
        GIT_REPO_URL = "https://github.com/raguyazhin/node-backend-app.git"
        GIT_REPO_BRANCH = "master"
        DOCKER_IMAGE_TAG = "${BUILD_NUMBER}"

    }
    stages {
        stage('Clone repository') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: "${GIT_REPO_BRANCH}"]],
                    userRemoteConfigs: [[url: "${GIT_REPO_URL}"]],
                    extensions: [[$class: 'CloneOption', depth: 1, shallow: true]]
                ])
            }
        }        
        stage('Build Docker image') {
            steps {
                script {
                    def workspacePath = env.WORKSPACE.replace(File.separator, "\\")
                    sh "docker build -t ${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG} ${workspacePath}/Dockerfile"
                }
            }
        }
        stage('Push Docker image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'ragudockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {                
                    sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD} ${DOCKER_REGISTRY}"
                    sh "docker push ${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}"
                }
            }
        }
    }
}
