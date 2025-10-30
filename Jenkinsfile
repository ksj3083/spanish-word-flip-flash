pipeline {
    agent any

    options {
        ansiColor('xterm')
    }

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:22-alpine'                    
                }
            }
            steps {
                sh 'npm ci'
                sh 'npm run build'
            }
        }

        stage('Test') {
            parallel {
                stage('Unit Tests') {
                    agent {
                        docker {
                            image 'node:22-alpine'
                            reuseNode true
                        }
                    }
                    steps {
                        // Unit tests with Vitest
                        sh 'npm install --save-dev vitest'
                        sh 'npx vitest run --reporter=verbose'                        
                    }
                }                
                stage('Integration Tests') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.54.2-jammy'
                            // image 'mcr.microsoft.com/playwright:v1.54.2-noble'
                            // image 'mcr.microsoft.com/playwright:v1.56.1-noble'                            
                            reuseNode true
                        }
                    }
                    steps {
                        // sh 'npm ci'
                        // sh 'npx playwright install --with-deps'
                        sh 'npx playwright test'
                        // Integration tests with Playwright
                        // sh 'npx playwright install --with-deps'
                        // sh 'npx playwright test --reporter=list'                        
                    }
                }
            }            
        }

        stage('Deploy') {
            agent {
                docker {
                    image 'alpine'                    
                }
            }
            steps {
                // Mock deployment which does nothing
                echo 'Mock deployment was successful!'                
            }
        }

        stage('e2e') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.54.2-jammy'
                    reuseNode true
                }
            }
            environment {
                E2E_BASE_URL = 'https://spanish-cards.netlify.app'
            }
            steps {                
                sh 'npx playwright test'
            }
            post {
                always {
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: true, icon: '', keepAll: false, reportDir: 'reports-e2e/html/', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectory: true])
                    junit stdioRetention: 'ALL', testResults: 'reports-e2e/junit.xml'
                }
            }
        }
    }
}