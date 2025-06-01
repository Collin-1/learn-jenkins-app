pipeline {
    agent any

    triggers {
        githubPush()
    }

    stages {
        stage('Package') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                    args '-u root'
                }
            }
            steps {
                sh '''
                echo "Starting packaging process..."

                # Move files to website folder
                apk update --quiet >/dev/null 2>&1
                apk add --no-cache --quiet zip >/dev/null 2>&1
                
                rm -f deployment.zip
                
                # Create zip
                zip -q -r deployment.zip build/*
                echo "Created deployment.zip with build folder contents"
                
                # Show the deployment file
                ls
                echo "Deployment package created successfully"
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
                echo "Starting deployment process..."
                
                # Clean up any existing temporary directory
                rm -rf /tmp/website-deploy
                
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
                
                echo "Files deployed to web directory"echo "Starting cleanup and verification..."
                
                # Verify deployment
                echo "Deployment completed. Files in website directory:"
                ls /var/www/html/
                
                # Clean up temporary directory
                rm -rf /tmp/website-deploy
                echo "Cleanup completed successfully"

                '''
            }
        }
    }
}
