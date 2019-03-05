pipeline {
  agent { label 'nodejs-app' }
  stages {
    stage('Say Hello') {
      steps {
        echo 'Hello World v1!'   
        sh 'java -version'
      }
    }
  }
}
