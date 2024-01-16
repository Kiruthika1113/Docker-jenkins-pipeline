pipeline {
  agent any

  stages {
    stage('Build') {
      steps {
        sh 'docker build -t my-flask .'
        sh 'docker tag my-flask $DOCKER_BFLASK_IMAGE'
      }
    }
    stage('Test') {
      steps {
        sh 'docker run my-flask python -m pytest app/tests/'
      }
    }
    stage('Deploy') {
      steps {
        withCredentials([usernamePassword(credentialsId: "${DOCKER_REGISTRY_CREDS}", passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
          sh "echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USERNAME --password-stdin docker.io"
          sh 'docker push $DOCKER_BFLASK_IMAGE'
        }
      }
    }
    
  }
   stage('Rollback') {
      steps {
        // Rollback to the previous version
        sh "docker pull $ROLLBACK_IMAGE"
        sh "docker tag $ROLLBACK_IMAGE my-flask"
        // You may need additional steps based on your deployment strategy
        // For example, restart the application, clear caches, etc.
      }
    }
  }


post{
      always{
            sh 'docker rm -f mypycont'
            sh 'docker run --name mypycont -d -p 3000:5000 my-flask'
            mail to: "kiruthikashanmugam23@gmail.com",
            subject: "rollback from jenkins",
            body: "rollback"
        }
   

  

