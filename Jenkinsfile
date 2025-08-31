pipeline {
    agent any

    stages {
        agent {
            docker {
                image 'node:18-alpine'
                reuseNode true
            }
        }
        stage('Build') {
            steps {
                sh '''
                    ls -al
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -al
                '''
            }
        }
        stage('Test') {
            steps {
                sh '''
                    npm run test
                '''
            }
        }
    }
}
