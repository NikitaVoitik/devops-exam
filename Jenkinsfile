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
                withKubeConfig([credentialsId: 'user1', serverUrl: 'https://k8s']) {
                      sh 'kubectl apply -f .'
                }

            }
        }
    }
}
