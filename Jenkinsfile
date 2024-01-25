pipeline {
   agent any
   environment {
        ENV = "dev"
        NODE = "k8s-build"
    }

   stages {
    stage('Build Image') {
        agent {
            node {
                label "k8s-build"
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

            sh "docker tag devops-training-nodejs-$ENV:latest robertkieu/devops-training:latest"

            //push docker image to docker hub
            sh "docker push robertkieu/devops-training:$TAG"
            sh "docker push robertkieu/devops-training:latest"

            

	    // remove docker image to reduce space on build server	
            sh "docker rmi -f robertkieu/devops-training:$TAG"

        }
         
       }
	    stage ("Deploy ") {
            agent {
                node {
                    label "k8s-build"
                }
            }
            environment {
                TAG = sh(returnStdout: true, script: "git rev-parse -short=10 HEAD | tail -n +2").trim()
            }
            steps {
                sh "kubectl delete pods -l app=nodejs-app -n api"
            }      
       }
   }
    
}
