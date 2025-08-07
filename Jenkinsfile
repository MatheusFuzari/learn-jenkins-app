pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '46a8cd99-81b4-4b7f-b6f1-3f9fa9631887'
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
                    ls -la
                    echo "Building..."
                    node --version
                    npm --version
                    npm ci --loglevel=verbose
                    npm run build --loglevel=verbose
                    ls -la
                '''
            }
        }

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
                    npm test 
                '''
            }

            post {
                always {
                    junit 'test-results/junit.xml'
                }
            }
        }

        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                    echo "Deploying to site ID: $NETLIFY_SITE_ID"
                '''
            }
        }
    }
}
