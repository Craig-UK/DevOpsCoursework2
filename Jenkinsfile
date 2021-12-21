pipeline {

    agent any
    stages {

        stage('Checkout Codebase'){
            steps{
                cleanWs()
                checkout scm: [$class: 'GitSCM', branches: [[name: '*/main']],userRemoteConfigs:
                [[credentialsId: 'github-ssh', url: 'git@github.com:Craig-UK/DevOpsCoursework2.git']]]
            }
        }
        
        stage('Build Image'){
            steps{
                sh 'docker image build --tag craiguk/devopscoursework2:3.0 .'
            }
        }

	stage('Push Image to Dockerhub') { 
            steps { 
                script { 
                    docker.withRegistry('', 'docker') { 
                        sh 'docker image push craiguk/devopscoursework2:3.0' 
                    }
                } 
            }
        }

        stage('Test'){
            steps{
                sh 'docker container run --detach --publish 64:64 --name devopscoursework2 craiguk/devopscoursework2:1.0'
            }
        }

        stage('Deploy App on k8s') {
            steps {
                sshagent(['k8s']) {
                    sh "scp -o StrictHostKeyChecking=no devops.yaml ubuntu@50.19.5.7:/home/ubuntu"
                script {
		    sh "ssh ubuntu@50.19.5.7 kubectl set image deployments/devopscoursework2 devopscoursework2=craiguk/devopscoursework2:3.0"
      	         }
	       }
            }
        }   
    }
}
