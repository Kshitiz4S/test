pipeline {
    agent any
	stages {
        stage('Checkout Code') {
            steps {
                // Checkout the repository code
                git 'https://github.com/Kshitiz4S/test.git'
            }
        }

        stage('Test') {
            steps {
                echo 'Testing...'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying...'
            }
        }
    }
}
