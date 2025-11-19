pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    ls -la
                    node --version
                    npm  --version
                    npm ci
                    npm run build
                    ls -la
                 '''
            }
        }

        stage('Test') {
            agent {
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    test -f build/index.html
                    npm test
                 '''
            }
        }

        stage('Deploy') {
            agent {
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                 '''
            }
        }

        stage('E2E') {
            agent {
                docker{
                    image 'mcr.microsoft.com/playwright:v1.56.1-noble'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install serve
                    node_modules/.bin/ -s build &
                    sleep 10
                    npx playwrite test --reporter=html
                 '''
            }
        }
    }
    post {
        always {
            junit 'test-results/junit.xml'
        }
    }
}
