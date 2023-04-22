pipeline {
    agent {
        label 'citibanknode'
    }
    environment{
        buildNumber = "BUILD_NUMBER"
    }
    stages{ 
        
    stage('checkout code') {
            steps{
                git branch: 'main', credentialsId: '8dbd25e4-e09c-4e66-8b3f-69dfa5533519', url: 'https://github.com/lokeshsgithub/Jenkins-Docker-K8s-Spring-boot-mongo.git'
            }
        }
      
    stage('Run UnitTest: Maven') {
          steps{
              sh "mvn test"
          }
      }  
    
    stage('Integration test cases: Maven') {
          steps{
              sh "mvn verify -DskipunitTests"
          }
      }   
    
    stage('Build the package: Maven') {
          steps{
              sh "mvn clean install"
          }
      } 
    
    stage('Building the Docker Image'){
        steps{
            sh "docker build -t lokeshsdockerhub/spring_boot:$BUILD_NUMBER ."
        }
    }
     stage('Building the Docker Image'){
        steps{
            sh "docker build -t lokeshsdockerhub/spring_boot:$BUILD_NUMBER ."
        }
    }
    
    stage('Push the image into Dockerhub'){
        steps{
            withCredentials([string(credentialsId: 'Dockerhubpwd', variable: 'dockerhubpwd')]) {
            sh "docker login -u lokeshsdockerhub -p ${dockerhubpwd}"
            sh "docker build -t lokeshsdockerhub/spring_boot:$BUILD_NUMBER ."
        }
    }
    }



    stage('Update the Image tag composefile'){
        steps{
            sh "sed -i 's/VERSION/${BUILD_NUMBER}/g' docker-compose.yml"
        }
    }
    
    stage('Deploy the Image to Docker stack'){
        steps{
            sshagent(['ubuntu_credentials']) {
           sh "scp -o stricthostkeychecking=no docker-compose.yml ubuntu@172.31.35.163:/home/ubuntu/docker-compose.yml"
           sh "ssh -o stricthostkeychecking=no ubuntu@172.31.35.163 docker stack deploy --compose-file docker-compose.yml spring_boot"
       }
    }
   
    }



    }

}