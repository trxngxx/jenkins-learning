pipeline {
   agent none
   environment {
        ENV = "dev"
        NODE = "Build-Server"
    }

   stages {
    stage('Build Image') {
        agent {
            node {
                label "Build-Server"
                customWorkspace "/home/ngotv/jenkins-$ENV/"
                }
            }
        environment {
            TAG = sh(returnStdout: true, script: "git rev-parse -short=10 HEAD | tail -n +2").trim()
        }
         steps {
            sh "docker build nodejs/. -t devops-training-nodejs-$ENV:latest --build-arg BUILD_ENV=$ENV -f nodejs/Dockerfile"


            sh "cat docker.txt | docker login -u 29trxngxx --password-stdin"
            // tag docker image
            sh "docker tag devops-training-nodejs-$ENV:latest 29trxngxx/devops-training"

            //push docker image to docker hub
            sh "docker push 29trxngxx/devops-training"

	    // remove docker image to reduce space on build server	
            sh "docker rmi -f 29trxngxx/devops-training"

           }
         
       }
	  stage ("Deploy ") {
	    agent {
        node {
            label "Target-Server"
                customWorkspace "/home/ubuntu/jenkins/multi-branch/devops-training-$ENV/"
            }
        }
        environment {
            TAG = sh(returnStdout: true, script: "git rev-parse -short=10 HEAD | tail -n +2").trim()
        }
	steps {
            sh "sed -i 's/{tag}/$TAG/g' /home/ubuntu/jenkins/multi-branch/devops-training-$ENV/docker-compose.yaml"
            sh "docker compose up -d"
        }      
       }
   }
    
}
