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
                    customWorkspace "/home/ubuntu/jenkins/"
                }
            }
            environment {
                TAG = ''
            }
            steps {
                script {
                    TAG = sh(returnStdout: true, script: "git rev-parse --short=10 HEAD | tail -n +2").trim()
                }
                sh """
                    docker build . -t devops-training-nodejs-${ENV}:latest --build-arg BUILD_ENV=${ENV} -f Dockerfile
                    cat docker.txt | docker login -u 29trxngxx --password-stdin
                    docker tag devops-training-nodejs-${ENV}:latest 29trxngxx/devops-training:${TAG}
                    docker push 29trxngxx/devops-training:${TAG}
                    docker rmi -f 29trxngxx/devops-training:${TAG}
                """
            }
        }
        
        stage('Deploy') {
            agent {
                node {
                    label "Target-Server"
                    customWorkspace "/home/ubuntu/jenkins"
                }
            }
            environment {
                TAG = ''
            }
            steps {
                script {
                    TAG = sh(returnStdout: true, script: "git rev-parse --short=10 HEAD | tail -n +2").trim()
                }
                sh """
                    docker network inspect app-network || docker network create app-network
                    sed -i 's/{tag}/${TAG}/g' /home/ubuntu/jenkins/docker-compose.yaml
                    docker-compose up -d
                """
            }
        }
    }
}
