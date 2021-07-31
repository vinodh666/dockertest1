pipeline {
  environment {
    registry = "sreeharshav/devopsb18"
    registryCredential = 'dockerhub_id'
    dockerSwarmManager = '10.40.1.96:2375'
    dockerhost = '10.40.1.96'
    dockerImage = ''
  }
  agent any
  stages {
    stage('Cloning our Git') {
      steps {
        git branch: 'DevOpsB18', url: 'https://github.com/mavrick202/dockertest1.git'
      }
    }
    stage('Building our image') {
      steps {
        script {
          dockerImage = docker.build registry + ":v$BUILD_NUMBER"
        }
      }
    }
    stage('Push Image To DockerHUB') {
      steps {
        script {
          docker.withRegistry('', registryCredential) {
            dockerImage.push()
          }
        }
      }
    }
    stage('Cleaning up') {
      steps {
        sh "docker rmi $registry:v$BUILD_NUMBER"
      }
    }
    stage('Deploying to Docker Swarm') {
      steps {
        sh "docker -H tcp://$dockerSwarmManager service rm testing1 || true"
        sh "docker -H tcp://$dockerSwarmManager service create --name testing1 -p 8100:80 $registry:v$BUILD_NUMBER"
      }
    }
    stage('Checking The WebApp Rechability') {
      steps {
        sh "sleep 10"
        sh "curl http://$dockerSwarmManager:8200 || exit 1 "
        }
    }
  }
}
