pipeline {
    agent {
        docker {
            image 'node:lts-bullseye-slim' 
            // Forçamos a desativação do TLS e limpamos o caminho do certificado nos argumentos do agente
            args '-v /var/run/docker.sock:/var/run/docker.sock --user root -e DOCKER_TLS_VERIFY=0 -e DOCKER_CERT_PATH='
        }
    }
    environment { 
        CI = 'true'
        NODE_OPTIONS = '--openssl-legacy-provider'
        // Definimos também no ambiente global do pipeline para garantir
        DOCKER_TLS_VERIFY = '0'
        DOCKER_CERT_PATH = ''
    }
    stages {
        stage('Check Environment') {
            steps {
                // Debug para confirmar que as variáveis estão corretas
                sh 'env | grep DOCKER || true'
            }
        }
        stage('Build') {
            steps {
                sh 'chmod -R +x ./jenkins/scripts'
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }      
        }
        stage('Deploy') {       
            steps {
                sh './jenkins/scripts/deliver.sh'
                input message: 'App rodando em http://localhost:3000. Clique para encerrar.'
                sh './jenkins/scripts/kill.sh'
            }
        }      
    }
}
