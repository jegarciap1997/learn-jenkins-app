pipeline {
    agent any
    stages {
        stage('w/o docker') {
            steps {
                sh '''
                    echo "without docker"
                    ls -ahl
                    touch container-no.txt
                '''
            }
        }
    }
}
