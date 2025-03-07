pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "my-jenkins-app"
        DOCKER_TAG = "latest"
        DOCKER_REPO = "mamatha0124/my-jenkins-app"
        DOCKER_CREDENTIALS_ID = "dckr_pat_yWxgRJd9pHl4fJONjrJXjs57GFI" // Jenkins credentials ID
        CONTAINER_NAME = "mycontainer11"
        CONTAINER_NAME1 = "mycontainer12"

    }
    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/Mamatha1206/docker-jenkins-app.git'
            }
        }
        stage('Docker Login') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        echo "Logged into Docker Hub"
                    }
                }
            }
        }
        stage('Docker Login') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dckr_pat_yWxgRJd9pHl4fJONjrJXjs57GFI') {
                        echo "Docker Login Successful"
                    }
                }
            }
        }

        stage('Run Container Locally') {
            steps {
                script {
                    sh """
                        # Stop and remove existing container if running
                        docker ps -a -q --filter name=${CONTAINER_NAME} | xargs -r docker stop || true
                        docker ps -a -q --filter name=${CONTAINER_NAME} | xargs -r docker rm || true

                        # Run new container
                        docker run -d -p 8095:80 --name ${CONTAINER_NAME} ${DOCKER_IMAGE}:${DOCKER_TAG}
                    """
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_REPO}:${DOCKER_TAG}"
                        sh "docker push ${DOCKER_REPO}:${DOCKER_TAG}"
                    }
                }
            }
        }
        stage('Deploy to Server') {
            steps {
                script {
                    sh """
                        sshpass -p "root" ssh -o StrictHostKeyChecking=no master@192.168.203.128 '
                        docker pull ${DOCKER_REPO}:${DOCKER_TAG} &&
                        docker ps -a -q --filter name=${CONTAINER_NAME1} | xargs -r docker stop || true &&
                        docker ps -a -q --filter name=${CONTAINER_NAME1} | xargs -r docker rm || true &&
                        docker run -d -p 80:80 --name ${CONTAINER_NAME1} ${DOCKER_REPO}:${DOCKER_TAG}'
                    """
                }
            }
        }
    }
}
