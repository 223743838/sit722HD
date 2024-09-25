pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'video-demo'
        DOCKER_TAG = 'latest'
        CONTAINER_REGISTRY = credentials('CONTAINER_REGISTRY')
        REGISTRY_UN = credentials('REGISTRY_UN')
        REGISTRY_PW = credentials('REGISTRY_PW')
    }

    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh 'npm run test'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $CONTAINER_REGISTRY/${DOCKER_IMAGE}:${DOCKER_TAG} .'
            }
        }
        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: CONTAINER_REGISTRY, passwordVariable: 'REGISTRY_PW', usernameVariable: 'REGISTRY_UN')]) {
                    sh 'docker login -u $REGISTRY_UN -p $REGISTRY_PW'
                    sh 'docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} $CONTAINER_REGISTRY/${DOCKER_IMAGE}:${DOCKER_TAG}'
                    sh 'docker push $CONTAINER_REGISTRY/${DOCKER_IMAGE}:${DOCKER_TAG}'
                }
            }
        }
        stage('Deploy') {
            steps {
                sh 'docker-compose up -d'
            }
        }
    }
}