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

    stage('Deploy staging') {
      environment {
        GIT_CREDS = credentials('github')
      }
      steps {
        container('tools') {
          sh "git clone https://$GIT_CREDS_USR:$GIT_CREDS_PSW@github.com/praveenvp07/kustomize-demo.git"
          sh "git config --global user.email 'praveen@cloudyuga.guru'"

          dir("kustomize-demo") {
            sh "git checkout 9-putting-it-all-together-fixing-labels"
            sh "cd ./overlays/staging && kustomize edit set image praveenvp07/rsvp-demo:${env.GIT_COMMIT}"
            sh "git commit -am 'Publish new version' && git push || echo 'no changes'"
          }
        }
      }
    }

    stage('Deploy to Prod') {
      steps {
        input message:'Approve deployment?'
        container('tools') {
          dir("kustomize-demo") {
            sh "cd ./overlays/prod && kustomize edit set image praveenvp07/rsvp-demo:${env.GIT_COMMIT}"
            sh "git commit -am 'Publish new version' && git push || echo 'no changes'"
          }
        }
      }
    }
  }
}
