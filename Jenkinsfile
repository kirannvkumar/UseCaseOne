
pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/kirannvkumar/UseCaseOne.git'
        FILE_MATCH = 'Akshay.txt'
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
                    } else {
                        error "No file with '${FILE_MATCH}' in its name was found."
                    }
                }
            }
    }
    }
}