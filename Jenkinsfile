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
        TAG = '1.0.1'
      }
      steps {
        withCredentials([usernamePassword(credentialsId: 'DockerToken', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASSWORD')]) {
          sh "docker build -t $DOCKER_REGISTRY/$IMAGE_NAME:$TAG ."
          sh "echo $DOCKER_PASSWORD | docker login -u $DOCKER_USER --password-stdin"
          sh "docker push $DOCKER_REGISTRY/$IMAGE_NAME:$TAG"
        }
      }
    }
    stage('Deploy') {
      environment {
        IMAGE_NAME = 'tcdmv/googlesearch'
        TAG = '1.0.1'
        SSH_USER = 'ec2-user'
        SSH_HOST = 'ec2-18-215-16-113.compute-1.amazonaws.com'
        SSH_PORT = '22'
      }
      steps {
        script{
          sshagent(['djkey']) {
          sh 'ssh -o StrictHostKeyChecking=no ec2-100-26-244-184.compute-1.amazonaws.com docker run -d -p 3080:3080 tcdmv/googlesearch:1.0.1'
        }
    }
  }
}
  }
}
