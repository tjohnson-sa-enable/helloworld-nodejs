library ‘cb-days@master’
pipeline {
  agent none
  options { 
    buildDiscarder(logRotator(numToKeepStr: '2'))
    skipDefaultCheckout true
  }
  triggers {
    eventTrigger simpleMatch('hello-api-deploy-event')
  }
  stages {
    stage('Web Tests') {
      agent {
        kubernetes {
          label 'nodejs-testcafe'
          yamlFile 'nodejs-pod.yaml'
        }
      }
      when {
        beforeAgent true
        branch 'development'
      }
      stages {
        stage('Nodejs Setup') {
          steps {
            checkout scm
            defineProps('.nodejs-app', [npmPackages: 'express pug'])            
            container('nodejs') {
              sh """
                npm i -S ${npmPackages}
                node ./hello.js &
              """
            }
          }   
        }
        stage('Testcafe') {
          steps {
            container('testcafe') {
              sh '/opt/testcafe/docker/testcafe-docker.sh "chromium --no-sandbox" tests/*.js -r xunit:res.xml'
            }
          }   
        }
      }  
      post {
        success {
          stash name: 'app', includes: '*.js, public/**, views/*, Dockerfile'
        }
        always {
          junit 'res.xml'
        }
      } 
    }
    stage('Build and Push Image') {
      when {
        beforeAgent true
        branch 'master'
      }
      steps {
        echo "TODO - build and push image"
      }
    }
  }
}
