pipeline {
   agent none
   environment {
        ENV = "dev"
        NODE = "Build-server"
    }

   stages {
    stage('Build Image') {
        agent {
            node {
                label "Build-server"
                customWorkspace "/home/qm/jenkins/"
                }
            }
        environment {
            TAG = sh(returnStdout: true, script: "git rev-parse -short=10 HEAD | tail -n +2").trim()
        }
         steps {
            sh "docker build . -t devops-training-nodejs-$ENV:latest --build-arg BUILD_ENV=$ENV -f Dockerfile"


            sh "cat docker.txt | docker login -u 29trxngxx --password-stdin"
            // tag docker image
            sh "docker tag devops-training-nodejs-$ENV:latest 29trxngxx/devops-training:$TAG"

            //push docker image to docker hub
            sh "docker push 29trxngxx/devops-training:$TAG"

	    // remove docker image to reduce space on build server	
            sh "docker rmi -f 29trxngxx/devops-training:$TAG"

           }
         
       }
	  stage ("Deploy ") {
	    agent {
        node {
            label "Target-Server"
                customWorkspace "/home/qm/jenkins-$ENV/"
            }
        }
        environment {
            TAG = sh(returnStdout: true, script: "git rev-parse -short=10 HEAD | tail -n +2").trim()
        }
	steps {
            sh "sed -i 's/{tag}/$TAG/g' /home/ubuntu/jenkins-$ENV/docker-compose.yaml"
            sh "docker-compose up -d"
        }      
       }
   }
    
}
