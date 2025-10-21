pipeline {
    agent any
    environment {
        NETLIFY_SITE_ID = 'f9ee86e7-62ce-4e8c-881a-1243849ea692'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token') // asignar variable de entorno mediante los secrets de Jenkins, estos se configuran desde el Manage Jenkins/Credentials/System/Global credentials el nombre del argumento de la funcion credentials debe ser el id que se asigno al momento de configurar el secret
    }
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
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
        stage('Tests') {
            parallel {  // ejecutar tareas en forma paralela ()stages
                stage('Test') {
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                            test -f build/index.html
                            npm run test
                        '''
                    }
                    post {
                        always {
                            junit 'jest-results/junit.xml' // referenciar archivo .xml con los resultados de las pruebas unitarias ejecutadas
                        }
                    }
                }
                stage('E2E') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy' // se instalo la imagen antes en el contenedor de jenkins, esto debido al tiempo que toma descargardo desde el agent
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                            npm install serve
                            node_modules/.bin/serve -s build &
                            sleep 10
                            npx playwright test --reporter=html
                        '''
                    }
                    post {
                        always {
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true]) // publicar el resultado de las pruebas p2p en un enlace que se accede desde jenkins
                        }
                    }
                }
            }
        }
        // Se puede ejecutar el post aqui o por cada stage...

        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            } 
            steps {
                sh '''
                    npm install netlify-cli@20.1.1
                    node_modules/.bin/netlify --version
                    echo "Deploying to production. site ID: $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }
        }
    }
}
