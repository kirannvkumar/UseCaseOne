
pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/kirannvkumar/UseCaseOne.git'
        FILE_MATCH = 'Akshay.txt'
        EC2_HOST = '54.208.53.48'
        SSH_CREDENTIALS_ID = 'MYSSH_CREDS'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: REPO_URL
                sh 'echo "Code checked out successfully!"'
            }
        }

        stage('Read github repo and check for File') {
            steps {
                script {
                    def foundFile = sh(script: "find . -type f -name '*${FILE_MATCH}*'", returnStdout: true).trim()

                    if (foundFile) {
                        echo "File(s) found: ${foundFile}"
                        // Install nginx server in ec2 instance
                        sshagent(credentials: [SSH_CREDENTIALS_ID]) {
                            sh """
                            ssh -o StrictHostKeyChecking=no ec2-user@${EC2_HOST} << EOF
                            sudo yum update -y
                            sudo yum install nginx -y
                            sudo systemctl start nginx
                            sudo systemctl enable nginx
                            sudo systemctl status nginx
                            EOF
                        """
                        }
                    }
                    else {
                        error "No file with '${FILE_MATCH}' in its name was found."
                    }
                }
            }
        }
    }
}
