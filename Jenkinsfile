pipeline {
    agent any

    environment{
        NETLIFY_SITE_ID= '618f277e-0e03-4838-98c8-854987c42118'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

    stages {
        stage('Docker'){
            steps{
                sh 'docker build -t node-alpine .'
            }
        }
        stage('Build') {
            agent {
                docker{
                    image 'node-alpine'
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
                    image 'node-alpine'
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

        //  stage('E2E') {
        //     agent {
        //         docker{
        //             image 'mcr.microsoft.com/playwright:v1.56.1-noble'
        //             reuseNode true
        //         }
        //     }
        //     steps {
        //         sh '''
        //             node ci
        //             npm install serve
        //             node_modules/.bin/serve -s build &
        //             sleep 10
        //             npx playwright test
        //          '''
        //     }
        //     post {
        //         always {
        //             junit 'test-results/junit.xml'
        //         }
        //      }
        // }

        stage('Deploy') {
            agent {
                docker{
                    image 'node-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    netlify --version
                    echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                    netlify status
                    netlify deploy --dir=build --prod --json > deploy-output.json
                    
                 '''
            }
        }

       
    }

}
