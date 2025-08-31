pipeline {
    agent {
        docker {
            image 'node:18-alpine'
            reuseNode true
        }
    }
    stages {
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
                    test  -f build/index.html
                    npm run test
                '''
            }
        }
    }
    post {
        always {
            junit 'test-results/junit.xml' // referenciar archivo .xml con los resultados de las pruebas unitarias ejecutadas
        }
    }
}
