pipeline {
    agent any

    environment {
        WORK_DIR = "/var/lib/jenkins/workspace/Game"
        IMAGE_NAME = "indie-gems"
        IMAGE_TAG  = "latest"
        CONTAINER_NAME = "indie-gems-container"
        PORT = "8888"   // External port for app
        DOCKERHUB_REPO = "moses777/indie-gems"  // your Docker Hub repo
    }

    stages {
        stage('Checkout Code') {
            steps {
                dir("${WORK_DIR}") {
                    git branch: 'main', url: 'https://github.com/prakash006602/Indie_Gems_Portal.git'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dir("${WORK_DIR}") {
                    sh '''
                        docker rmi -f ${IMAGE_NAME}:${IMAGE_TAG} || true
                        docker build -t ${IMAGE_NAME}:${IMAGE_TAG} -f ${WORK_DIR}/Dockerfile ${WORK_DIR}
                    '''
                }
            }
        }

        stage('Docker Hub Login & Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'moses', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${DOCKERHUB_REPO}:${IMAGE_TAG}
                        docker push ${DOCKERHUB_REPO}:${IMAGE_TAG}
                    '''
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                sh '''
                    docker rm -f ${CONTAINER_NAME} || true
                    docker run -d --name ${CONTAINER_NAME} -p ${PORT}:80 ${DOCKERHUB_REPO}:${IMAGE_TAG}
                '''
            }
        }
    }
}
