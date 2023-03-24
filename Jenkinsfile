pipeline {
  agent any
  
  stages {
    stage('Build') {
      steps {
        sh 'npm install'
        sh 'npm run build'
      }
    }
    stage('Docker Build and Push') {
      environment {
        DOCKER_REGISTRY = 'tcdmv'
        IMAGE_NAME = 'googlesearch'
        TAG = '1.0.0'
      }
      steps {
        withCredentials([usernamePassword(credentialsId: 'tcdmvText', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASSWORD')]) {
          sh "docker build -t $DOCKER_REGISTRY/$IMAGE_NAME:$TAG ."
          sh "docker login -u $DOCKER_USER -p $DOCKER_PASSWORD $DOCKER_REGISTRY"
          sh "docker push $DOCKER_REGISTRY/$IMAGE_NAME:$TAG"
        }
      }
    }
    stage('Deploy') {
      environment {
        IMAGE_NAME = 'tcdmv/googlesearch'
        TAG = '1.0.0'
      }
      steps {
        sh "docker-compose pull"
        sh "docker-compose up -d"
      }
    }
  }
}
