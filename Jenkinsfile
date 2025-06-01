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
                echo building
                ls -la
                '''
            }
        }

        stage('Test'){
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            
            steps {
            sh '''
            echo testing
            '''
            }
        }

        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                    args '-u root'
                }
            }
            steps {
                sh '''
                echo deploying
                apk update --quiet >/dev/null 2>&1
                apk add --no-cache --quiet zip >/dev/null 2>&1
                
                # Create zip with less verbose output
                zip -q -r deployment.zip . -x "*.log" "test-results/*" "node_modules/*"
                
                # Only show the deployment file
                ls -la deployment.zip
                pwd
                echo "Deployment package created"

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
