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
                withKubeConfig([credentialsId: 'laborant', serverUrl: 'https://k8s:6443']) {
                      sh '/usr/local/bin/kubectl apply -f pod.yaml'
                      sh '/usr/local/bin/kubectl wait --for=condition=Ready pod/my-app --timeout=60s'
                      sh '/usr/local/bin/kubectl apply -f service.yaml'
                }

            }
        }
    }
}
