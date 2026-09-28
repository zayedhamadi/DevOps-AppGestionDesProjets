pipeline {
  agent any
  environment {
    DOCKERHUB_USER = 'zayehamadi'
    TAG = "${env.BUILD_NUMBER}"
  }
  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Build images') {
      steps {
        sh 'docker build -t $DOCKERHUB_USER/projets-backend:$TAG -t $DOCKERHUB_USER/projets-backend:latest ./backend'
        sh 'docker build -t $DOCKERHUB_USER/projets-frontend:$TAG -t $DOCKERHUB_USER/projets-frontend:latest ./frontend'
      }
    }
    stage('Push images') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                         usernameVariable: 'U', passwordVariable: 'P')]) {
          sh 'echo "$P" | docker login -u "$U" --password-stdin'
          sh 'docker push $DOCKERHUB_USER/projets-backend:$TAG'
          sh 'docker push $DOCKERHUB_USER/projets-backend:latest'
          sh 'docker push $DOCKERHUB_USER/projets-frontend:$TAG'
          sh 'docker push $DOCKERHUB_USER/projets-frontend:latest'
        }
      }
    }
    stage('Deploy') {
      steps {
        sh 'docker compose down || true'
        sh 'docker compose up -d --build'
      }
    }
  }
  post {
    always { sh 'docker logout || true' }
  }
}
