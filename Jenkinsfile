pipeline {

  environment {
    registry = "172.16.0.109:31320/drupal"
    dockerImage = ""
  }

 agent any
  
  stages {

    stage('Checkout Source') {
      agent { label 'kubepod' }
      steps {
        
        git 'https://github.com/ArtemOnishchuk/project_ci-cd.git'
      }
    }

    stage('Build image') {
      agent { label 'kubepod' }
      steps {  
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
          
        }  
      }
    }

    stage('Push Image') {
      agent { label 'kubepod' }
      steps{
        script {
          docker.withRegistry( "" ) {
            dockerImage.push()
          }
        }
      }
    }

    stage('Deploy App') {
      agent { label 'kubepod' }
      steps {
        script {
          kubernetesDeploy(configs: "drupal.yaml", kubeconfigId: "mykubeconfig")
        }
      }
    }

  }
    post {
        success {
            slackSend (color: '#00FF00', message: "[SUCCESS] Job: ${env.JOB_NAME} ID: ${env.BUILD_ID} URL: ${env.BUILD_URL}")
        }

        failure {
            slackSend (color: '#FF0000', message: "[FAILURE] Job: ${env.JOB_NAME} ID: ${env.BUILD_ID} URL: ${env.BUILD_URL}")
        }
    
    }

}
