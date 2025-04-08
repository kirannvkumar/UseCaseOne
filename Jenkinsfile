pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/kirannvkumar/UseCaseOne.git'
        FILE_MATCH = 'Akshay.txt'
        EC2_HOST = '54.208.53.48'
        SSH_CREDENTIALS_ID = 'MYSSH_CREDS'
        AWS_ACCESS_KEY_ID = credentials('aws-access-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
        AWS_REGION = 'us-east-1'
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
                        // Install nginx server in EC2 instance
                        sshagent(credentials: [SSH_CREDENTIALS_ID]) {
                            sh """
                                ssh -o StrictHostKeyChecking=no ec2-user@${EC2_HOST} << 'EOF'
                                    sudo yum update -y
                                    sudo yum install nginx -y

                                    # Stop any process using port 80
                                    sudo fuser -k 80/tcp || true

                                    sudo systemctl start nginx
                                    sudo systemctl enable nginx
                                    sudo systemctl status nginx
EOF
                            """
                        }
                    } else {
                        echo "No file with '${FILE_MATCH}' found, installing httpd server..."
                        // Install httpd server in EC2 instance
                        sshagent(credentials: [SSH_CREDENTIALS_ID]) {
                            sh """
                                ssh -o StrictHostKeyChecking=no ec2-user@${EC2_HOST} << 'EOF'
                                    sudo yum update -y
                                    sudo yum install -y httpd
                                    sudo systemctl start httpd
                                    sudo systemctl enable httpd
                                    echo "<h1>Hello World from \$(hostname -f)</h1>" | sudo tee /var/www/html/index.html
EOF
                            """
                        }
                    }
                }
            }
        }

        stage('Check Route 53 DNS') {
            steps {
                echo "Checking Route 53 hosted zones and DNS records..."
                script {
                    try {
                        sh """
                            export AWS_REGION=${AWS_REGION}
                            aws configure set aws_access_key_id "$AWS_ACCESS_KEY_ID"
                            aws configure set aws_secret_access_key "$AWS_SECRET_ACCESS_KEY"
                            aws configure set default.region "$AWS_REGION"

                            ZONE_ID=\$(aws route53 list-hosted-zones --query "HostedZones[0].Id" --output text | cut -d'/' -f3)
                            echo "Using Hosted Zone ID: \$ZONE_ID"

                            aws route53 list-resource-record-sets --hosted-zone-id \$ZONE_ID
                        """
                    } catch (err) {
                        error "Failed to fetch DNS records: ${err}"
                    }
                }
            }
        }
    }
}