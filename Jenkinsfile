pipeline {
    agent any

    tools {
        nodejs "Node 22"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh "npm test"
            }
        }
        stage('Build') {
            steps {
                sh "docker build . --tag ttl.sh/nodejs11:1h"
                sh "docker push ttl.sh/nodejs11:1h"
            }
        }
        stage('Deploy') {
            steps {
                withCredentials([sshUserPrivateKey(
                    credentialsId: '485e9257-7c6e-411e-9487-267a87234a20',
                    keyFileVariable: 'ssh_key',
                    usernameVariable: 'ssh_user')]) {
                        sh """
mkdir -p ~/.ssh
ssh-keyscan docker >> ~/.ssh/known_hosts

ssh -i ${ssh_key} ${ssh_user}@docker 'docker stop nodejs || true'

ssh -i ${ssh_key} ${ssh_user}@docker 'docker rm nodejs || true'

ssh -i ${ssh_key} ${ssh_user}@docker 'docker pull ttl.sh/nodejs11:1h'

ssh -i ${ssh_key} ${ssh_user}@docker 'docker run -d -p 4444:4444 --name nodejs ttl.sh/nodejs11:1h '
"""
                }
            }
        }
    }
}
