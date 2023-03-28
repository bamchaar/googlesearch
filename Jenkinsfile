pipeline {
  agent any
  
  stages {
    stage('Build') {
                      steps {
                        echo 'image was build'
                            }
                   }
    stage('Docker Build and Push') {
                          environment {
                            DOCKER_REGISTRY = 'tcdmv'
                            IMAGE_NAME = 'googlesearch'
                            TAG = '1.0.3'
                                      }
                      steps {
                          withCredentials([usernamePassword(credentialsId: 'DockerToken', usernameVariable: 'DockerId', passwordVariable: 'DOCKER_PASSWORD')]) {
                            sh "docker build -t $DOCKER_REGISTRY/$IMAGE_NAME:$TAG ."
                            sh "echo $DOCKER_PASSWORD | docker login -u tcdmv --password-stdin"
                              }
                            }
                                    }
    stage('Deploy') {
                        environment {
                          IMAGE_NAME = 'tcdmv/googlesearch'
                          TAG = '1.0.3'
                          SSH_USER = 'ec2-user'
                          SSH_PORT = '22'
                                   }
                      steps {
                        script {
                          withCredentials([sshUserPrivateKey(credentialsId: 'ec2-user-server', keyFileVariable: 'SSH_KEY_FILE', passphraseVariable: '', usernameVariable: 'SSH_USER')]) {
                              sh """
                                  chmod 0600 \$SSH_KEY_FILE
                                  ssh -i \$SSH_KEY_FILE \$SSH_USER@3.82.171.111
                                  docker run -d -p 3080:3080 tcdmv/googlesearch:1.0.3
                              """
                          }
                             }      
                   }
  }
}
