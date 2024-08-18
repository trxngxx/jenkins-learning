pipeline {
   agent none
   environment {
        ENV = "dev"
        NODE = "Build-server"
    }

	  stage ("Deploy ") {
	    agent {
        node {
            label "Target-Server"
                customWorkspace "/home/ubuntu/jenkins"
            }
        }
        environment {
            TAG = sh(returnStdout: true, script: "git rev-parse -short=10 HEAD | tail -n +2").trim()
        }
	steps {
            sh "sed -i 's/{tag}/$TAG/g' /home/ubuntu/jenkins/docker-compose.yaml"
            sh "docker-compose up -d"
        }      
       }
   }
    

