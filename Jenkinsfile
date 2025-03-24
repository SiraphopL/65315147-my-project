pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '422eca98-96f3-426e-b73c-d78752479732'
        NETLIFY_AUTH = credentials('netlify-token')
    }

    stages {
        stage('Install Dependencies') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh 'npm ci'
            }
        }

        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh 'npm run build'
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
                    mkdir -p test-results
                    npm test -- --ci --reporters=default --reporters=jest-junit
                '''
            }
        }

        stage('Deploy to Netlify') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npx netlify deploy --dir=build --prod --site=$NETLIFY_SITE_ID --auth=$NETLIFY_AUTH
                '''
            }
        }
    }

    post {
        always {
            script {
                try {
                    junit 'test-results/junit.xml'
                } catch (Exception e) {
                    echo "Test report missing or failed: ${e.message}"
                }
            }
        }
    }
}
