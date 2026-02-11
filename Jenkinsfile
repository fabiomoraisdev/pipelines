pipeline {
    agent {
        docker {
            image 'node:lts-bullseye-slim' 
            // Adicionamos a montagem do socket aqui também para que o contêiner 'node' 
            // possa executar comandos docker se necessário (como o kill.sh ou outros)
            args '-p 3000:3000 -v /var/run/docker.sock:/var/run/docker.sock --user root'
        }
    }
    environment { 
        CI = 'true'
        // Necessário para versões recentes do Node.js com pacotes antigos
        NODE_OPTIONS = '--openssl-legacy-provider'
    }
    stages {
        stage('Build') {
            steps {
                // Garantir que as permissões dos scripts estejam corretas antes de começar
                sh 'chmod -R +x ./jenkins/scripts'
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }      
        }

        stage('Deploy for production') {       
            steps {
                // O deliver.sh inicia o app em background
                sh './jenkins/scripts/deliver.sh'
                
                // O input pausa o pipeline para você ver o resultado
                input message: 'O aplicativo está rodando em http://localhost:3000. Clique em Proceed para encerrar.'
                
                // O kill.sh encerra o processo do node
                sh './jenkins/scripts/kill.sh'
            }
        }      
    }
}
