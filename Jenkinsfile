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
        TAG = '1.0.2'
      }
      steps {
        withCredentials([usernamePassword(credentialsId: 'DockerToken', usernameVariable: 'DockerId', passwordVariable: 'DOCKER_PASSWORD')]) {
          sh "docker build -t $DOCKER_REGISTRY/$IMAGE_NAME:$TAG ."
          sh "echo $DOCKER_PASSWORD | docker login -u $DOCKER_USER --password-stdin"
          sh "docker push $DOCKER_REGISTRY/$IMAGE_NAME:$TAG"
        }
      }
    }
    stage('Deploy') {
      environment {
        IMAGE_NAME = 'tcdmv/googlesearch'
        TAG = '1.0.2'
        SSH_USER = 'ec2-user'
        SSH_PORT = '22'
      }
      steps {
        script{
          sshagent(['ssh-key']) {
          sh 'ssh -o StrictHostKeyChecking=no ec2-user@100.26.244.184 docker run -d -p 3080:3080 tcdmv/googlesearch:1.0.1'
        }
    }
  }
}
  }
}
