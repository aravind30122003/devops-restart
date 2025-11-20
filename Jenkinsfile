pipeline {
 agent any
 stages {
   stage('Clone Repository') {
            steps {
                git url: 'https://github.com/aravind30122003/devops-restart', branch: 'main' 
     }
   }
   stage('listing the directory'){
           steps{
              sh 'ls -h'
    } 
  } 
}
   post {
      success {
         echo "pipeline succeeded"
    }
      failure {
         echo "pipeline failed"
    }
  }
}

