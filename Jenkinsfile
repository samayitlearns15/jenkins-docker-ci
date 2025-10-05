pipeline {
  agent any

  environment {
    DOCKERHUB_USER = "samayitlearns15"
    IMAGE_NAME = "jenkins-docker-ci"
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/samayitlearns15/jenkins-docker-ci.git'
      }
    }

    stage('Build Image') {
      steps {
        sh 'docker build -t $DOCKERHUB_USER/$IMAGE_NAME:latest .'
      }
    }

    stage('Run Container (smoke test)') {
      steps {
        sh '''
          if docker ps -a --format '{{.Names}}' | grep -w flask-app; then
            docker rm -f flask-app || true
          fi
          docker run -d -p 5000:5000 --name flask-app $DOCKERHUB_USER/$IMAGE_NAME:latest
        '''
      }
    }

    stage('Push to Docker Hub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', usernameVariable: 'DH_USER', passwordVariable: 'DH_PASS')]) {
          sh '''
            echo $DH_PASS | docker login -u $DH_USER --password-stdin
            docker push $DH_USER/$IMAGE_NAME:latest
            docker logout
          '''
        }
      }
    }
  }
}
