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
        ROUTE53_DOMAIN = 'nvtesting.shop' // Replace with your Route53 domain
        HOSTED_ZONE_ID = 'Z0262530JEOOHHQSAMX3'    // Replace with your hosted zone ID
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: REPO_URL
                sh 'echo "Code checked out successfully!"'
            }
        }

        stage('Read github repo, check for Filename and after validation, install nginx or httpd accordingly') {
    steps {
        script {
            def foundFile = sh(script: "find . -type f -name '*${FILE_MATCH}*'", returnStdout: true).trim()

            if (foundFile) {
                echo "File(s) found: ${foundFile}"
                // Install nginx server
                sshagent(credentials: [SSH_CREDENTIALS_ID]) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ec2-user@${EC2_HOST} << 'EOF'
                            # Check if httpd is installed
                            if rpm -q httpd >/dev/null 2>&1; then
                                echo "httpd is installed. Stopping it..."
                                sudo systemctl stop httpd
                                sudo systemctl disable httpd
                            fi

                            sudo yum update -y
                            sudo yum install -y nginx
                            sudo fuser -k 80/tcp || true
                            sudo systemctl start nginx
                            sudo systemctl enable nginx
EOF
                    """
                }
            } else {
    echo "No file with '${FILE_MATCH}' found, installing httpd server..."
    sshagent(credentials: [SSH_CREDENTIALS_ID]) {
        sh """
            ssh -o StrictHostKeyChecking=no ec2-user@${EC2_HOST} << 'EOF'
                # Check if nginx is installed
                if rpm -q nginx; then
                    echo "Nginx is installed. Stopping nginx..."
                    sudo systemctl stop nginx
                    sudo systemctl disable nginx
                else
                    echo "Nginx is not installed. Proceeding..."
                fi

                # Install and start httpd
                sudo yum install -y httpd
                sudo systemctl start httpd
                sudo systemctl enable httpd

                # Add a simple index.html
                echo "<h1>Hello World from \$(hostname -f)</h1>" | sudo tee /var/www/html/index.html
EOF
        """
    }
}

        }
    }
}
        stage('Create/Update Route53 Record') {
            steps {
                script {
                    def changeBatch = """
                    {
                      "Comment": "Update record to point to EC2",
                      "Changes": [{
                        "Action": "UPSERT",
                        "ResourceRecordSet": {
                          "Name": "${ROUTE53_DOMAIN}",
                          "Type": "A",
                          "TTL": 300,
                          "ResourceRecords": [{ "Value": "${EC2_HOST}" }]
                        }
                      }]
                    }
                    """

                    writeFile file: 'change-batch.json', text: changeBatch

                    sh """
                        aws route53 change-resource-record-sets \
                          --hosted-zone-id ${HOSTED_ZONE_ID} \
                          --change-batch file://change-batch.json \
                          --region ${AWS_REGION}
                    """
                }
            }
        }

        stage('Access Nginx via Route53 Domain') {
            steps {
                script {
                    try {
                        echo "Waiting for DNS propagation..."
                        sleep time: 30, unit: 'SECONDS'
                        echo "Attempting to curl http://${ROUTE53_DOMAIN}"
                        
                        sh "curl --fail --connect-timeout 10 --retry 3 --retry-delay 5 http://${ROUTE53_DOMAIN}"
                        echo "✅ Success: Able to access Nginx via Route53!"
                    } catch (Exception e) {
                        echo "❌ Failed to access Nginx at ${ROUTE53_DOMAIN}"
                    }
                }
            }
        }
    }
}