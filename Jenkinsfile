pipeline {
    agent any

    environment {
        DOCKERHUB_USERNAME = "chakisuk"
        IMAGE_NAME = "farmdora-FE"
        DOCKER_IMAGE = "${DOCKERHUB_USERNAME}/${IMAGE_NAME}"
        DOCKER_TAG = "latest"
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-jenkins')
        CONTAINER_NAME = "farmdora-FE"
    }

    stages {
        stage('Build') {
            steps {
                sh 'npm install'
                sh 'CI=false npm run build'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .'
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([string(credentialsId: '${DOCKER_HUB_TOKEN}', usernameVariable: 'username', passwordVariable: 'password')]) {
                    sh 'docker login -u ${DOCKERHUB_USERNAME} --password-stdin'
                    sh 'docker push ${DOCKER_IMAGE}:${DOCKER_TAG}'
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker stop ${CONTAINER_NAME} || true'
                sh 'docker rm ${CONTAINER_NAME} || true'
                sh 'docker run -d -p 80:80 -p 443:443 --name ${CONTAINER_NAME} ${DOCKER_IMAGE}:${DOCKER_TAG}'
            }
        }
    }

    post {
        always {
            sh 'docker logout'
        }
    }
}