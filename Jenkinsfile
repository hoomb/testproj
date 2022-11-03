pipeline {
    agent any

    stages {
        stage('check java') {
            steps {
                sh 'java -version'
            }
        }
        stage('clean') {
            steps {
                sh 'chmod +x mvnw'
                sh './mvnw -ntp clean -P-webapp'
            }
        }

        stage('install tools') {
            steps {
                sh './mvnw -ntp com.github.eirslett:frontend-maven-plugin:install-node-and-npm -DnodeVersion=v14.15.0 -DnpmVersion=6.14.11'
            }
        }

        stage('npm install') {
            steps {
                sh './mvnw -ntp com.github.eirslett:frontend-maven-plugin:npm'
            }
        }

        stage('packaging') {
            steps {
                sh './mvnw -ntp verify -P-webapp -Pprod -DskipTests'
                archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
            }
        }

        stage('publish docker') {
            steps {
                sh './mvnw -ntp jib:buildTar'
            }
        }
    }
}
