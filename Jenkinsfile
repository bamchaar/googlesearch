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
      }
      steps {
        script{
          def DockerCmd = "docker run -p 3080:3080 -d $IMAGE_NAME:$TAG "
        sshagent(['ec2-jenkins-docker-server']) {
                 sh "ssh -o StrictHostKeyChecking=no ec2-user@54.237.5.9 $DockerCmd"

                }
        }
      }
    }
  }
}
