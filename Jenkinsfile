@Library('jenkins-shared-repo')
def gv

pipeline {
    agent any
    options {
        skipStagesAfterUnstable()
    }
    tools{
    maven 'mvn-3.9'
    }
    environment{
    	NEW_VERSION = '2.0'
    }
    stages {
        stage('Build image') {
            steps {
                script{
		    buildImagePet()
                }
            }
        }        
        stage('Deploy') {
                	when{
        		expression{
        		   env.BRANCH_NAME == 'master'
        		}
        	}        
        	input{
        		message "Select the environment to deploy"
        		ok "Done"
        		parameters{
        		choice(name: 'environment1', choices: ['dev', 'staging', 'prod'], description:'')
        		choice(name: 'environment2', choices: ['dev', 'staging', 'prod'], description:'')
        		}
        	} 
            steps {
				script{
				echo " deploy ${environment1}"
				echo " deploy ${environment2}"
			}
            }
        }
        stage('Deliver') { 
                	when{
        		expression{
        			env.BRANCH_NAME == 'master'
        		}
        	}        
            steps {
			script{
				env.REPO = input message:"Select the repo to deliver to", ok: "Done", parameters:[choice(name: 'environment1', choices: ['DockerHub', 'Nexus', 'AWS-ECR'], description:'')]
				echo "Deliver to ${REPO}"
				}
            }
        }
    }
} 
