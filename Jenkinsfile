pipeline {
    agent {
      kubernetes  {
            label 'jenkins-slave'
        }
    }
 
  stages {

    stage('Build') {
      environment {
        DOCKERHUB_CREDS = credentials('dockerhub')
      }
      steps {
        container('docker') {
          // Build new image
          sh "until docker ps; do sleep 3; done && docker build -t praveenvp07/rsvp-demo:${env.GIT_COMMIT} ."
          // Publish new image
          sh "docker login --username $DOCKERHUB_CREDS_USR --password $DOCKERHUB_CREDS_PSW && docker push praveenvp07/rsvp-demo:${env.GIT_COMMIT}"
        }
      }
    }
  }
}
