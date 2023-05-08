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
