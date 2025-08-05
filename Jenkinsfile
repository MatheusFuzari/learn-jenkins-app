pipeline {
    agent any

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
    }
}
