pipeline {
    agent any
    options {
        // This is required if you want to clean before build
        skipDefaultCheckout(true)
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
                checkout scm
                echo "Building ${env.JOB_NAME}..."
            }
        }
        
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

        stage('run application') {
            steps {
                sh """
                docker-compose -f "${WORKSPACE}/src/main/docker/app.yml" rm -sfv testproj-app1 testproj-app2 testproj-nginx || true
                docker rmi testproj -f || true
                docker load --input "${WORKSPACE}/target/jib-image.tar"
                docker-compose -f "${WORKSPACE}/src/main/docker/app.yml" up -d
                """
            }
        }
        stage('run monitoring') {
            steps {
                sh """
                docker-compose -f "${WORKSPACE}/src/main/docker/monitoring.yml" rm -sfv testproj-prometheus testproj-grafana || true
                docker-compose -f "${WORKSPACE}/src/main/docker/monitoring.yml" up -d
                """
            }
        }
    }
}
 