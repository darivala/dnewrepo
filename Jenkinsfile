pipeline{
  agent { node { label 'MyAgents' } } 
  environment {
    IMAGE_TAG = "${env.BUILD_NUMBER}"
    myImageName = "mydocimg"
    myDockerhub = "darivala"
  }
  stages{
    stage('SCM Checkout'){
     steps{
          git 'https://github.com/darivala/dnewrepo.git'
          }
  }

    stage('Unit Testing'){
     steps{
             sh label: '', script: 'mvn clean test'
     }
   }
    stage('Maven packing'){
     steps{
           sh label: '', script: 'mvn clean package'
        }
    }

    stage('Build & Deploy New Image'){
     steps{
        withCredentials([usernamePassword(credentialsId: 'dockerHub',usernameVariable: 'dockerHubUser', passwordVariable: 'dockerHubPassword')]){
        script {
          sh '''
           sudo docker login -u ${dockerHubUser} -p ${dockerHubPassword} docker.io
           sudo docker build -t docker.io/${myDockerhub}/${myImageName}:${IMAGE_TAG} .
           sudo docker push docker.io/${myDockerhub}/${myImageName}:${IMAGE_TAG} 
           sudo docker images
           sudo docker ps -f name=${myImageName} -q | xargs --no-run-if-empty sudo docker container stop
           sudo docker container ls -a -fname=${myImageName} -q | xargs -r sudo docker container rm
           sudo docker system prune -f
           sudo docker run -d -p 8082:8080 --name ${myImageName} ${myDockerhub}/${myImageName}:${IMAGE_TAG}
           sudo docker logout 
           '''
        }
       }
     }
   }
  }
}
