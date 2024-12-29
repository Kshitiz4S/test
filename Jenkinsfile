pipeline {
    agent any
   
    stages {
        stage('GIT SCM Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/staging']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Kshitiz4S/test.git']])
                echo 'SCM Checkout Successful'
            }
        }
        stage('Docker Compose') {
            steps {
                sh "docker-compose up -d"
            }
        }
        stage('Push Image to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId:'DockerHub',passwordVariable:'DOCKER_PASSWORD',usernameVariable:'DOCKER_USERNAME')]) {
                sh "docker tag nextjs-application-app:latest ${env.DOCKER_USERNAME}/nextjs-app-dev:v1.0"
                sh "docker tag postgres:15-alpine ${env.DOCKER_USERNAME}/nextjs-db-dev:v1.0"
                sh "docker login -u ${env.DOCKER_USERNAME} -p ${env.DOCKER_PASSWORD}"
                sh "docker push ${env.DOCKER_USERNAME}/nextjs-app-dev:v1.0"
                sh "docker push ${env.DOCKER_USERNAME}/nextjs-db-dev:v1.0"
                echo 'Push successful'
                }
            }
        }
        stage('Deployment') {
            steps {
                sh "docker-compose down && docker-compose up -d"
            }
        }
    }
}

