pipeline {
    agent any

    environment {
        DOCKER_HUB_USERNAME = 'kshitiz182'   // Set this as your Docker Hub username
        APP_IMAGE_NAME = 'nextjs-app'        // Name for the app Docker image (e.g., "myapp")
        DB_IMAGE_NAME = 'nextjs-db'          // Name for the database Docker image (e.g., "mydb")
        IMAGE_TAG = "V:${BUILD_NUMBER}"      // Tag to push, you can use something dynamic like "build-${BUILD_NUMBER}" if you prefer
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/Kshitiz4S/test.git'
	       }
        }

	stage('Deploy with Docker Compose') {
	    steps {
        	script {
            	sh 'docker-compose up -d'
       		}
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
                        sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD'
                    }
                }
            }
        }

	stage('Push Docker Images to Docker Hub') {
            steps {
                script {
                    sh "docker tag ${APP_IMAGE_NAME}:latest ${DOCKER_HUB_USERNAME}/${APP_IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker push ${DOCKER_HUB_USERNAME}/${APP_IMAGE_NAME}:${IMAGE_TAG}"

                    sh "docker tag ${DB_IMAGE_NAME}:latest ${DOCKER_HUB_USERNAME}/${DB_IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker push ${DOCKER_HUB_USERNAME}/${DB_IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }

        stage('Clean Up') {
            steps {
                sh 'docker rmi ${APP_IMAGE_NAME}:latest'
                sh 'docker rmi ${DB_IMAGE_NAME}:latest'
            }
        }
    }

    post {
        always {
            sh 'docker system prune -f'
        }
    }
}

