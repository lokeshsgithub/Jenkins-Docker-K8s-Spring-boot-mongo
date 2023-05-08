@Library('lokeshsharedlibs') _
pipeline {

agent {
    label 'citibanknode'
}
tools {
    maven 'maven3.9.0'
}
environment {
    buildNumber = "BUILD_NUMBER"
}

stages {

    stage('Checkout code') {
        steps {
            sendSlackNotifications('STARTED')
            git branch: 'main', credentialsId: '8dbd25e4-e09c-4e66-8b3f-69dfa5533519', url: 'https://github.com/lokeshsgithub/Jenkins-Docker-K8s-Spring-boot-mongo.git'
        }
    }

    stage('Run unit tests: Maven'){
        steps{
            sh "mvn test"
        }
    }

    stage('Integration of test cases : Maven') {
        steps{
            sh "mvn verify -DskipunitTests"
        }
    }
    
    stage('build & SonarQube analysis'){
        steps{
            withSonarQubeEnv(credentialsId: 'sonarqube_credential',installationName: 'sonarqube') {
                sh "mvn clean package sonar:sonar"
         }
      }
    }

    stage("Quality Gate") {
            steps {
              timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
              }
            }
    }

    stage('Build the docker image: Docker') {
        steps{
            sh "docker build -t lokeshsdockerhub/springapp:$BUILD_NUMBER ."
        }
    }
    
    stage('scan the image: Trivy'){
        steps{
            sh "trivy image lokeshsdockerhub/springapp:$BUILD_NUMBER"
        }
    }

    stage('Push the image : Dockerhub'){
        steps{
            sh "docker push lokeshsdockerhub/springapp:$BUILD_NUMBER"
        }
    }
    
    stage('Deploy the docker image into the k8s cluster'){
        steps{
            sh " kubectl apply -f mongo.yml"
            sh "kubectl apply -f springapp.yml"
        }
    }

}//stages closed

post{
    success{
        sendSlackNotifications(currentBuild.result)
    }
    failure{
        sendSlackNotifications(currentBuild.result)
    }
}

}//pipeline closed
