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
                        sh 'npx vitest run --reorter=verbose'                        
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
    }
}