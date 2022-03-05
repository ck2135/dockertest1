pipeline {
    agent any


    environment {
      DOCKER_TAG = getVersion()
    }

    stages {

        stage('Clone Repo') {
          steps {
            sh 'rm -rf dockertest1/'
            sh 'git clone https://github.com/ck2135/dockertest1.git'
          }
        }
        
        stage('Build Docker Image') {
          steps {  
            sh 'cd /var/lib/jenkins/workspace/pipeline2/dockertest1'
            sh 'cp /var/lib/jenkins/workspace/pipeline2/dockertest1/* /var/lib/jenkins/workspace/pipeline2'
            sh 'rm -rf /var/lib/jenkins/workspace/pipeline2/dockertest1/*'
            sh 'docker build -t charan2135/pipelinetest2:${DOCKER_TAG} .'
          }
        }
            
        stage('Push Image to Docker Hub') {
          steps {  
            sh 'docker push charan2135/pipelinetest2:${DOCKER_TAG}'
          }
        }
            
        stage('Deploy to Docker Host') {
          steps {
            sh '''if [ ! "$(docker -H tcp://10.0.0.250:2375 ps -q -f name=webapp)" ]; then
    if [ "$(docker -H tcp://10.0.0.250:2375 ps -aq -f status=exited -f name=webapp)" ]; then
        # cleanup
        docker -H tcp://10.0.0.250:2375 rm webapp1
    fi
    # run your container
    docker -H tcp://10.0.0.250:2375 run -dit --name webapp1 --hostname webapp1 -p 5000:80 charan2135/pipelinetest2:${DOCKER_TAG}
fi'''
          } 
        }
            
        stage('Check WebApp Reachablity') {
          steps {
            sh 'sleep 10s'
            sh 'curl http://ec2-13-126-54-65.ap-south-1.compute.amazonaws.com:5000'
          } 
        }
    }
}



def getVersion() {
  def commitHash = sh returnStdout: true, script: 'git rev-parse --short HEAD'
  return commitHash
}
