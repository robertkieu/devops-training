pipeline {
   agent any
   environment {
        ENV = "dev"
        NODE = "mac-build"
    }

   stages {
    stage('Build Image') {
        agent {
            node {
                label "mac-build"
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
                    label "mac-deploy"
                }
            }
            environment {
                TAG = sh(returnStdout: true, script: "git rev-parse -short=10 HEAD | tail -n +2").trim()
            }
            steps {
                sh "docker container prune -f"
                sh "docker-compose -f /Users/kieuduckhuong/jenkins_deploy/workspace/devops-training/docker-compose.yaml up -d"
            }      
       }
   }
    
}
