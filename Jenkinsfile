@Library('github.com/releaseworks/jenkinslib') _

pipeline {
    agent any
    environment {
        registry = "246697379410.dkr.ecr.us-east-1.amazonaws.com/project"
    }

    stages {
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/surajvenkatesh/Jenkins_task.git']]])
            }
        }

    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry
        }
      }
    }

    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
        steps{
            script {
                sh 'docker login -u AWS -p $(aws ecr get-login-password --region us-east-1) 246697379410.dkr.ecr.us-east-1.amazonaws.com/project '
                sh 'docker push 246697379410.dkr.ecr.us-east-1.amazonaws.com/project'
            }
        }
    }

    stage('Docker Run') {
     steps{
         script {
             sshagent(credentials : ['aws_ec2']){

                sh 'ssh -o StrictHostKeyChecking=no -i Suraj.pem ubuntu@10.0.2.101'

             }
                //sh 'ssh -i /login/Suraj.pem ubuntu@10.0.2.101'
                sh 'docker run -d -p 8081:8080 --rm --name node 246697379410.dkr.ecr.us-east-1.amazonaws.com/project'
            }
      }
    }
    }
}