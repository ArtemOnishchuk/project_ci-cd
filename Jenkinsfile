pipeline {

  environment {
    registry = "172.16.0.109:31320/drupal"
    dockerImage = ""
  }

 agent any
  
  stages {

    stage('Checkout Source') {
      steps {
        
        git 'https://github.com/ArtemOnishchuk/project_ci-cd.git'
      }
    }

    stage('Build image') {
      steps {  
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
          
        }  
      }
    }

    stage('Push Image') {
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

}
