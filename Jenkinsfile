pipeline {
    agent any

    stages {
        stage('check java') {
            steps {
                sh 'java -version'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}