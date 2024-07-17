pipeline {
  agent any
  triggers {
    cron '0 2 * * 6' // 2:00 AM Saturday, Melbourne time
  }
  stages {
    stage('Build Image') {
      steps {
        sh "podman build --no-cache --target sources --tag quay.io/kjtsanaktsidis/ruby-rr-ci/sources:${env.GIT_COMMIT} ."
        sh "podman build --no-cache --tag quay.io/kjtsanaktsidis/ruby-rr-ci:${env.GIT_COMMIT} ."
      }
    }
    stage('Push Image (commit)') {
      steps {
        withCredentials([file(credentialsId: 'podman-auth.json', variable: 'REGISTRY_AUTH_FILE')]) {
          sh "podman push quay.io/kjtsanaktsidis/ruby-rr-ci/sources:${env.GIT_COMMIT}"
          sh "podman push quay.io/kjtsanaktsidis/ruby-rr-ci:${env.GIT_COMMIT}"
        }
      }
    }
    stage('Push Image (latest)') {
      when {
          branch 'main'
      }
      steps {
        withCredentials([file(credentialsId: 'podman-auth.json', variable: 'REGISTRY_AUTH_FILE')]) {
          sh "podman tag quay.io/kjtsanaktsidis/ruby-rr-ci/sources:${env.GIT_COMMIT} quay.io/kjtsanaktsidis/ruby-rr-ci/sources:latest"
          sh "podman tag quay.io/kjtsanaktsidis/ruby-rr-ci:${env.GIT_COMMIT} quay.io/kjtsanaktsidis/ruby-rr-ci:latest"
          sh "podman push quay.io/kjtsanaktsidis/ruby-rr-ci/sources:latest"
          sh "podman push quay.io/kjtsanaktsidis/ruby-rr-ci:latest"
        }
      }
    }
  }
}
