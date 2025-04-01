pipeline {
    agent any

    environment {
        DOCKERHUB_USERNAME = "chakisuk"
        IMAGE_NAME = "test-front"
        DOCKER_IMAGE = "${DOCKERHUB_USERNAME}/${IMAGE_NAME}"
        DOCKER_TAG = "latest"
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-jenkins')
        CONTAINER_NAME = "test-front"
    }


    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', 
                   url: 'https://github.com/chakisuk/front-test.git',
                   credentialsId: 'farmdora-login'  // 필요한 경우
            }
        }

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
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin' // docker hub 로그인
                sh 'docker push ${DOCKER_IMAGE}:${DOCKER_TAG}'
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