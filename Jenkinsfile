
pipeline {
    agent any

    parameters {
            base64File name: 'my_file', description: 'My Uploaded File'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/kirannvkumar/UseCaseOne.git'
                sh 'echo "Code checked out successfully!"'
            }
        }

    // Checks whether the file name has the text "Akshay.txt" or not
    stage('Upload and Check') {
                  steps {
                      withFileParameter(name: 'MY_FILE', allowNoFile: false) {
                          sh """
                              # Check if the file exists
                              if [ -f "${MY_FILE}" ]; then
                                  # Get the filename
                                  filename="${MY_FILE##*/}"

                                  # Check if the filename is what you expect
                                  if [ "${filename}" == "Akshay.txt" ]; then
                                      echo "File name is correct: ${filename}"
                                  else
                                      echo "File name is incorrect: ${filename}"
                                      echo "Expected Filename is: Akshay.txt"
                                      fail "File name verification failed"
                                  fi
                              else
                                  echo "File not found"
                                  fail "File upload failed"
                              fi
                          """
                      }
                  }
    }
    }
}
