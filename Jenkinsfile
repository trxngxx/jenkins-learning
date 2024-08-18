pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/trxngxx/jenkins-learning.git'
            }
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
    
    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
    

