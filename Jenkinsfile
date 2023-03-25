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
        TAG = '1.0.0'
        DOCKER_IMAGE = 'tcdmv/googlesearch:tag'
        SSH_PRIVATE_KEY = credentials('DockerToken')
        SSH_USER = 'ec2-user'
        SSH_HOST = 'ec2-18-215-16-113.compute-1.amazonaws.com'
        SSH_PORT = '22'
        APP_DIRECTORY = '/var/www/html'
      }
      steps {
        script{
          def DockerCmd = "docker run -p 3080:3080 -d $IMAGE_NAME:$TAG "
          withCredentials([usernamePassword(credentialsId: 'DockerToken', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASSWORD')]) {
          sh "docker build -t $DOCKER_REGISTRY/$IMAGE_NAME:$TAG ."
          sh "echo $DOCKER_PASSWORD | docker login -u $DOCKER_USER --password-stdin"
          }
        withCredentials([sshUserPrivateKey(credentialsId: 'DockerToken', keyFileVariable: 'SSH_PRIVATE_KEY', passphraseVariable: '', usernameVariable: 'SSH_USER')]) {
          sh '''
            docker build -t $DOCKER_REGISTRY/$IMAGE_NAME:$TAG
            echo $DOCKER_PASSWORD | docker login -u $DOCKER_USER --password-stdin
            docker pull $DOCKER_IMAGE
            ssh -o StrictHostKeyChecking=no -i $SSH_PRIVATE_KEY $SSH_USER@$SSH_HOST docker run  -p 3080:3080 -d ${DOCKER_IMAGE}
          '''       
      }
    }
  }
}
