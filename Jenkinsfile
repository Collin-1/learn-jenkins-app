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
                
                rm -f deployment.zip
                rm -rf /tmp/website-deploy

                # Create zip with less verbose output
                cd build/
                zip -q -r ../deployment.zip .
                ls
                cd ..
                echo "Created deployment.zip with build folder contents"
                
                
                # Only show the deployment file
                ls
                echo "Deployment package created"

                # Create a temporary directory for extraction
                mkdir -p /tmp/website-deploy
                
                # Unzip the deployment package
                echo "Extracting deployment package..."
                unzip -q deployment.zip -d /tmp/website-deploy
                
                ls /tmp/website-deploy

                # Create website directory if it doesn't exist
                mkdir -p /var/www/html
                
                # Move files to website folder
                echo "Moving files to website directory..."
                cp -r /tmp/website-deploy/* /var/www/html/
                
                
                # Set proper permissions (optional)
                chmod -R 755 /var/www/html
                
                # Verify deployment
                echo "Deployment completed. Files in website directory:"
                ls /var/www/html/
                
                # Clean up temporary directory
                rm -rf /tmp/website-deploy
                echo "Cleanup completed"
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
