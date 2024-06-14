pipeline {
  agent none
  environment {
      DOCKERHUB_CREDENTIALS = credentials('DockerLogin')
  }
  stages {
    stage('Build') {
      agent {
        docker {
          image 'node:lts-buster-slim'
        }
      }
      steps {
        sh 'npm install'
      }
    }
    stage('Test'){
      agent {
        docker {
          image 'node:lts-buster-slim'
        }
      }
      steps {
        sh 'echo test'
      }
    }
    stage('Build Docker Image') {
            agent {
                docker {
                    image 'docker:dind'
                    args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
                sh 'docker build -t xenjutsu/nodejsgoof:0.1 .'
            }
        }
        stage('Push Image to Docker Registry') {
            agent {
                docker {
                    image 'docker:dind'
                    args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh 'docker push xenjutsu/nodejsgoof:0.1'
            }
        }
    stage('Deploy Docker Image') {
            agent {
                docker {
                    image 'kroniak/ssh-client'
                }
            }
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: "RnD", keyFileVariable: 'keyfile')]) {
                    sh 'ssh -i ${keyfile} -o StrictHostKeyChecking=no root@139.162.18.93 "echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin"'
                    sh 'ssh -i ${keyfile} -o StrictHostKeyChecking=no root@139.162.18.93 docker pull xenjutsu/nodejsgoof:0.1'
                    sh 'ssh -i ${keyfile} -o StrictHostKeyChecking=no root@139.162.18.93 docker rm --force mongodb'
                    sh 'ssh -i ${keyfile} -o StrictHostKeyChecking=no root@139.162.18.93 docker run --detach --name mongodb -p 27017:27017 mongo:3'
                    sh 'ssh -i ${keyfile} -o StrictHostKeyChecking=no root@139.162.18.93 docker rm --force nodejsgoof'
                    sh 'ssh -i ${keyfile} -o StrictHostKeyChecking=no root@139.162.18.93 docker run -it --detach -p 3001:3001 --name nodejsgoof --network host xenjutsu/nodejsgoof:0.1'
                }
            }
        }
  }
}
