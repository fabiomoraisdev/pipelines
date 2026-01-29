pipeline {
    agent any

    environment {
        CI = 'true'
    }

    stages {
        stage('Build') {
            steps {
                sh '''
                  docker run --rm \
                    -v "$PWD:/app" \
                    -w /app \
                    node:lts-bullseye-slim \
                    npm install
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                  docker run --rm \
                    -v "$PWD:/app" \
                    -w /app \
                    node:lts-bullseye-slim \
                    chmod +x ./jenkins/scripts/test.sh && \
                    ./jenkins/scripts/test.sh
                '''
            }
        }

        stage('Deliver for development') {
            when {
                branch 'develop'
            }
            steps {
                sh '''
                  docker run --rm \
                    -v "$PWD:/app" \
                    -w /app \
                    node:lts-bullseye-slim \
                    chmod -R +x ./jenkins/scripts && \
                    ./jenkins/scripts/deliver.sh
                '''
                input message: 'Finished using the web site? (Click "Proceed" to continue)'
                sh './jenkins/scripts/kill.sh'
            }
        }

        stage('Deploy for production') {
            steps {
                sh '''
                  docker run --rm \
                    -v "$PWD:/app" \
                    -w /app \
                    node:lts-bullseye-slim \
                    chmod -R +x ./jenkins/scripts && \
                    ./jenkins/scripts/deliver.sh
                '''
                input message: 'Finished using the web site? (Click "Proceed" to continue)'
                sh './jenkins/scripts/kill.sh'
            }
        }
    }
}
