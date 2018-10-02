pipeline {
  options {
    disableConcurrentBuilds()
  }
  agent {
    label "jenkins-maven"
  }
  environment {
    DEPLOY_NAMESPACE = "jx-staging"
  }
  stages {
    stage('Validate Environment') {
      steps {
        container('maven') {
          dir('env') {
            sh 'wget https://gist.githubusercontent.com/kokwai/fe352638f8f2495176e337a9c7a1efa3/raw/103e34e0a66fe3cc53b8738c67a4b9896ce9cae3/helm_wrapper.sh'
            sh 'chmod 755 helm_wrapper.sh'
            sh 'which helm'
            sh 'jx edit helmbin helm_wrapper.sh'
            sh 'jx step helm build'
          }
        }
      }
    }
    stage('Update Environment') {
      when {
        branch 'master'
      }
      steps {
        container('maven') {
          dir('env') {
            sh 'jx step helm apply'
          }
        }
      }
    }
  }
}
