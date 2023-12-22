pipeline {
   agent any
   environment {
        ENV = "dev"
        NODE = "build-server"
    }

   stages {
    stage('Build Image') {
        agent {
            node {
                label "build-server"
            }
        }
        environment {
            TAG = sh(returnStdout: true, script: "git rev-parse -short=10 HEAD | tail -n +2").trim()
        }
         steps {
            sh "docker build nodejs/. -t devops-training-nodejs-$ENV:latest --build-arg BUILD_ENV=$ENV -f nodejs/Dockerfile"


            sh "cat docker.txt | docker login -u robertkieu --password-stdin"
            // tag docker image
            sh "docker tag devops-training-nodejs-$ENV:latest robertkieu/devops-training:$TAG"

            //push docker image to docker hub
            sh "docker push robertkieu/devops-training:$TAG"

	    // remove docker image to reduce space on build server	
            sh "docker rmi -f robertkieu/devops-training:$TAG"

        }
         
       }
	    stage ("Deploy ") {
            agent {
                node {
                    label "build-server"
                }
            }
            environment {
                TAG = sh(returnStdout: true, script: "git rev-parse -short=10 HEAD | tail -n +2").trim()
            }
            steps {
                sh "sed -i 's/{tag}/$TAG/g' /var/jenkins_home/workspace/devops-training/docker-compose.yaml"
                sh "docker container prune -f"
                sh "docker compose up -d"
            }      
       }
   }
    
}
