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
	//     stage ("Deploy ") {
    //         agent {
    //             node {
    //                 label "build-server"
    //             }
    //         }
    //         environment {
    //             TAG = sh(returnStdout: true, script: "git rev-parse -short=10 HEAD | tail -n +2").trim()
    //         }
    //         steps {
    //             sh "sed -i 's/{tag}/$TAG/g' /var/jenkins_home/workspace/devops-training/docker-compose.yaml"
    //             sh "docker stop app"
    //             sh "docker container prune -f"
    //             sh "docker compose up -d"
    //         }      
    //    }
   }
    
}
