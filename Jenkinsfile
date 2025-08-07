pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '46a8cd99-81b4-4b7f-b6f1-3f9fa9631887'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
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
            parallel {
                stage('Unit test') {
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

                stage('E2E') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.54.0-jammy'
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
                    node_modules/.bin/netlify --status
                '''
            }
        }
    }
}
