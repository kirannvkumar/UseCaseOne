
pipeline {
    agent any
    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/kirannvkumar/UseCaseOne.git'
                sh 'echo "Code checked out successfully!"'
            }
        }

        parameters {
            base64File name: 'my_file', description: 'My Uploaded File'
        }

        stage('Upload and Process') {
            steps {
                withFileParameter(name: 'my_file', allowNoFile: false) {
                    // Access the uploaded file using the parameter name
                    sh "ls -l ${my_file}" // Example: List the contents of the file
                    // Add your logic to process the uploaded file here
                }
            }
        }

        // stage('Read File') {
        //         steps {
        //             // Read the file
        //             script {
        //                 def fileContent = readFile file: 'path/to/your/file.txt' // Replace with the file path
        //                 echo "File content: ${fileContent}"
        //             }
        //         }
        // }
    }
}
