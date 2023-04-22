pipeline {

    agent any

    environment {

        DOCKER_REGISTRY = "raguyazhin"
        DOCKER_IMAGE_NAME = "node-backend-app"
        DOCKER_IMAGE_TAG = "${BUILD_NUMBER}.0.0"

        APP_GIT_REPO_URL = "https://github.com/raguyazhin/node-backend-app.git"
        APP_GIT_REPO_BRANCH = "master"

        KUBE_MANIFEST_GIT_REPO_URL = "https://github.com/raguyazhin/kube-manifest-node-backend-app.git"
        KUBE_MANIFEST_GIT_REPO_BRANCH = "master"
        KUBE_MANIFEST_FILE = "node-backend-deployment.yaml"

    }
    stages {

        stage('Clone App repository') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: "${APP_GIT_REPO_BRANCH}"]],
                    userRemoteConfigs: [[url: "${APP_GIT_REPO_URL}"]],
                    extensions: [[$class: 'CloneOption', depth: 1, shallow: true]]
                ])    
            }
        }     
           
        stage('Build Docker image') {
            steps {
                script {
                    def workspacePath = env.WORKSPACE.replace(File.separator, "\\\\")
                    sh "docker build -t ${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG} ${workspacePath}"
                }
            }
        }

        // stage('Push Docker image') {
        //     steps {
        //         withCredentials([usernamePassword(credentialsId: 'ragudockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {                
        //             sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
        //             sh "docker push ${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}"
        //         }
        //     }
        // }

        stage('Clone Kube Manifest repository') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: "${KUBE_MANIFEST_GIT_REPO_BRANCH}"]],
                    userRemoteConfigs: [[url: "${KUBE_MANIFEST_GIT_REPO_URL}"]],
                    extensions: [[$class: 'CloneOption', depth: 1, shallow: true]]
                ])
            }
        }  

        // Plugin - Pipeline Utility Steps
        stage('Update image in kube manifest') {
            steps {
               script {
                    def workspacePath = env.WORKSPACE.replace(File.separator, "\\\\")
                    def yaml = readYaml(file: "${workspacePath}\\\\${KUBE_MANIFEST_FILE}")
                    sh "echo ${yaml}"
                    yaml.spec.template.spec.containers[0].image = "${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}"
                    writeYaml(file: "${workspacePath}\\\\${KUBE_MANIFEST_FILE}", data: yaml, overwrite: true )
                    def yaml1 = readYaml(file: "${workspacePath}\\\\${KUBE_MANIFEST_FILE}")
                    sh "echo ${yaml1}"
                }
            }
        }   
    }
}
