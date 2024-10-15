pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    # Check Node.js and npm versions
                    node --version
                    npm --version

                    # Install project dependencies
                    npm ci

                    # Build the project
                    npm run build

                    # List files in the current directory to verify build output
                    ls -la
                '''
            }
        }

        stage('Test') {
            agent {
                docker {
                    image 'node:18'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    # Check Node.js and npm versions
                    node --version
                    npm --version

                    # Install project dependencies (if not already installed)
                    npm ci

                    # Run tests
                    npm test
                '''
            }
        }

        stage('E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    # Install serve package
                    npm install serve

                    # Serve the build directory
                    node_modules/.bin/serve -s build &

                    # Wait for the server to start
                    sleep 10

                    # Run Playwright tests
                    npx playwright test --reporter=html
                '''
            }
        }
    }

    post {
        always {
            junit 'jest-results/junit.xml'
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
        }
    }
}