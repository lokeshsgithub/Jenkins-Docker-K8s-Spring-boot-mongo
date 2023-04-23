pipeline {
    agent {
        label 'citibanknode'
    }
    environment{
        buildNumber = "BUILD_NUMBER"
    }
    tools {
        maven 'maven3.9.0'
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
              sh "mvn clean package"
          }
      } 
    
    stage('Building the Docker Image'){
        steps{
            sh "docker build -t 800161990735.dkr.ecr.ap-south-1.amazonaws.com/spring-boot:$BUILD_NUMBER ."
        }
    }

    stage('Scan the image: trivy') {
          steps{
              sh "trivy image 800161990735.dkr.ecr.ap-south-1.amazonaws.com/spring-boot:$BUILD_NUMBER > scan.txt"
              sh "cat scan.txt"
          }
      }


    stage('Push the image into ECR Registry'){
        steps{
            sh "aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 800161990735.dkr.ecr.ap-south-1.amazonaws.com"
            sh "docker push 800161990735.dkr.ecr.ap-south-1.amazonaws.com/spring-boot:$BUILD_NUMBER"
        }
    }
    
    stage('Update the Image tag : composefile') {
          steps{
              sh "sed -i 's/VERSION/${BUILD_NUMBER}/g' composefile.yml"
          }
      }

    stage('Deploy the Image to Docker stack'){
        steps{
            sshagent(['ubuntu_credentials']) {
           sh "aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 800161990735.dkr.ecr.ap-south-1.amazonaws.com"
           sh "scp -o stricthostkeychecking=no compose.yml ubuntu@172.31.35.163:"
           sh "ssh -o stricthostkeychecking=no ubuntu@172.31.35.163 docker stack rm spring_boot"
           sh "ssh -o stricthostkeychecking=no ubuntu@172.31.35.163 docker stack deploy --compose-file compose.yml spring_boot"
       }
    }
   
    }
    
    }

}