pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = 'c592958d-b048-45e3-902d-d56f1e549b2d'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
        REACT_APP_VERSION = "1.0.$BUILD_ID"
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
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }

        stage('Run Unit Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "Unit Test Local"
                    test -f build/index.html
                    npm test
                '''
            }
            post {
                always {
                    junit 'jest-test-results/junit.xml'
                }
            }
        }

        stage('Deploy production') {
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
                    echo "Deploy to production site ID = $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    
                '''
                sh 'node_modules/.bin/netlify deploy --dir=build --prod' 
            }
        }
    }
}