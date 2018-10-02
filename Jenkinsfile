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
            sh 'curl -kLo helm-linux-amd64-v2.9.1.tar.gz https://9.30.118.226:8443/api/cli/helm-linux-amd64.tar.gz'
            sh 'tar -xvf helm-linux-amd64-v2.9.1.tar.gz'
            sh 'cp -f linux-amd64/helm /usr/bin/helm'
            sh 'wget https://gist.githubusercontent.com/kokwai/fe352638f8f2495176e337a9c7a1efa3/raw/103e34e0a66fe3cc53b8738c67a4b9896ce9cae3/helm_wrapper.sh'
            sh 'chmod 755 helm_wrapper.sh'
            sh 'which helm'
            sh 'helm init --client-only --skip-refresh'
            sh 'curl -kLo cloudctl https://9.30.118.226:8443/api/cli/cloudctl-linux-amd64'
            sh 'chmod 755 cloudctl'
            sh './cloudctl login --skip-ssl-validation -a https://9.30.118.226:8443 -u admin -p admin -c id-mycluster-account -n default'
            sh 'cp /home/jenkins/.cloudctl/clusters/mycluster/cert.pem /home/jenkins/.helm/cert.pem'
            sh 'cp /home/jenkins/.cloudctl/clusters/mycluster/key.pem /home/jenkins/.helm/key.pem'
            sh 'helm version --tls'
            sh './helm_wrapper.sh version'
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
