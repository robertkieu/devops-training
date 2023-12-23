pipeline {
   agent any
   environment {
        ENV = "dev"
        NODE = "build-node"
    }

   stages {
    stage('Build Image') {
        agent {
            node {
                label "build-node"
            }
        }
        environment {
            TAG = sh(returnStdout: true, script: "git rev-parse -short=10 HEAD | tail -n +2").trim()
        }
         steps {
            sh "sudo docker build nodejs/. -t devops-training-nodejs-$ENV:latest --build-arg BUILD_ENV=$ENV -f nodejs/Dockerfile"


            sh "sudo cat docker.txt | sudo docker login -u robertkieu --password-stdin"
            // tag docker image
            sh "sudo docker tag devops-training-nodejs-$ENV:latest robertkieu/devops-training:$TAG"

            //push docker image to docker hub
            sh "sudo docker push robertkieu/devops-training:$TAG"

	    // remove docker image to reduce space on build server	
            sh "sudo docker rmi -f robertkieu/devops-training:$TAG"

        }
         
       }
	    stage ("Deploy ") {
            agent {
                node {
                    label "deploy-node"
                }
            }
            environment {
                TAG = sh(returnStdout: true, script: "git rev-parse -short=10 HEAD | tail -n +2").trim()
            }
            steps {
                sh "sudo sed -i 's/{tag}/$TAG/g' /home/ubuntu/jenkins_deploy/workspace/devops-training/docker-compose.yaml"
                sh "sudo docker container prune -f"
                sh "sudo docker compose up -d"
            }      
       }
   }
    
}
