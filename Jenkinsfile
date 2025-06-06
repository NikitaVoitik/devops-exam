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
        stage('Deploy') {
            steps {
                withCredentials([sshUserPrivateKey(
                    credentialsId: '485e9257-7c6e-411e-9487-267a87234a20',
                    keyFileVariable: 'ssh_key',
                    usernameVariable: 'ssh_user')]) {
                        sh """
mkdir -p ~/.ssh
ssh-keyscan target >> ~/.ssh/known_hosts

ssh -i ${ssh_key} ${ssh_user}@target 'mkdir -p /home/laborant/app'

scp -i ${ssh_key} package*.json index.js index.test.js ${ssh_user}@target:/home/laborant/app/

ssh -i ${ssh_key} ${ssh_user}@target 'cd /home/laborant/app'

ssh -i ${ssh_key} ${ssh_user}@target 'sudo systemctl stop node-app.service || true'

ssh -i ${ssh_key} ${ssh_user}@target 'cd /home/laborant/app && npm install'

ssh -i ${ssh_key} ${ssh_user}@target 'cd /home/laborant/app && (npm start&)'
"""
                }
            }
        }
    }
}
