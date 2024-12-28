pipeline {
    agent any

    environment {
        DOCKER_HUB_USERNAME = 'kshitiz182'   // Set this as your Docker Hub username
        APP_IMAGE_NAME = 'nextjs-app'        // Name for the app Docker image (e.g., "myapp")
        DB_IMAGE_NAME = 'nextjs-db'          // Name for the database Docker image (e.g., "mydb")
        IMAGE_TAG = "V:${BUILD_NUMBER}"      // Tag to push
    }

    stages {
        stage('GIT SCM Checkout') {
            steps {
                git branch: 'staging', url: 'https://github.com/Kshitiz4S/test.git'
        }

	stage('Deploy with Docker Compose') {
	    steps {
	         	sh 'sudo docker-compose up -d'
    	    }
	}	

        stage('Build App Docker Image') {
            steps {
                script {
                    docker.build(APP_IMAGE_NAME, '-f Dockerfile .')
                }
            }
        }

        stage('Build DB Docker Image') {
            steps {
                script {
                    docker.build(DB_IMAGE_NAME, '-f Dockerfile.db .') 
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-id', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh 'sudo docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD'
                    }
                }
            }
        }

	stage('Push Docker Images to Docker Hub') {
            steps {
                script {
                    sh "sudo docker tag ${APP_IMAGE_NAME}:latest ${DOCKER_HUB_USERNAME}/${APP_IMAGE_NAME}:${IMAGE_TAG}"
                    sh "sudo docker push ${DOCKER_HUB_USERNAME}/${APP_IMAGE_NAME}:${IMAGE_TAG}"

                    sh "sudo docker tag ${DB_IMAGE_NAME}:latest ${DOCKER_HUB_USERNAME}/${DB_IMAGE_NAME}:${IMAGE_TAG}"
                    sh "sudo docker push ${DOCKER_HUB_USERNAME}/${DB_IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }

        stage('Clean Up') {
            steps {
                sh 'sudo docker rmi ${APP_IMAGE_NAME}:latest'
                sh 'sudo docker rmi ${DB_IMAGE_NAME}:latest'
            }
        }
    }

    stage('Deploy Test') {
            steps {
                echo 'Connecting to test server'
                sh """
                    ssh vagrant@192.168.56.33 "
                    
                    sudo apt update &&
                    sudo apt install docker.io -y
                    
                    sudo docker pull kshitiz182/nextjs-app:v1
                    
                    sudo docker run -idt -p 3000:3000 --name Nextjs-Project kshitiz182/nextjs-app:v1
                    "
                """
            }
        }
    }
}


