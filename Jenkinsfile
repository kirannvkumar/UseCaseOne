pipeline {
  agent any
  stages {
    stage('Checkout Code') {
      steps {        
        git branch: 'main', url: 'https://github.com/kirannvkumar/UseCaseOne.git'
        sh 'echo "Code checked out successfully!"'
      }
    }
  }
}


// pipeline {
//   agent any
//   stages {
//     stage('Checkout Code') {
//       steps {
//         git 'https://github.com/kirannvkumar/UseCaseOne.git'
//       }
//     }
//     stage('Build') {
//       steps {
//         sh 'mvn clean install' // Example Maven build command
//       }
//     }
//     stage('Test') {
//       steps {
//         sh 'mvn test' // Example Maven test command
//       }
//     }
//     stage('Deploy') {
//       when {
//         branch 'main' // Deploy only on the main branch
//       }
//       steps {
//         sh 'echo "Deploying to production..."' // Replace with your deployment command
//       }
//     }
//   }
// }


