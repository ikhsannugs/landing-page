pipeline {
  agent any 
  triggers {
        pollSCM(env.GIT_BRANCH == 'master' ? '* * * * *' : env.GIT_BRANCH == 'staging' ? '* * * * *' : '')
    }
  stages {
      stage('Checkout SCM') {
        steps{
          checkout scm
        }
      }
      stage('Git Check') {
        steps{
          sh "ls"
          sh "git --version"
          echo "Deployment TO ${env.GIT_BRANCH}"
          script {   env.DOCKER_REGISTRY = 'ikhsannugs'
                     env.DOCKER_IMAGE_NAME = 'big-project'
                     env.DOCKER_IMAGE_APPS = 'landpage'                    
          }
        }
      }
      stage('Build Docker Image') {
        steps{
          if( env.GIT_BRANCH == 'master' ){
            sh "docker build -t $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:${DOCKER_IMAGE_APPS}_staging_${BUILD_NUMBER}."
            sh "docker push $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:${DOCKER_IMAGE_APPS}_staging_${BUILD_NUMBER}"
            echo "Docker Image Build For Server Stagging Success"
          }
          else if ( env.GIT_BRANCH == 'staging' ){
            sh "docker build -t $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:${DOCKER_IMAGE_APPS}_production_${BUILD_NUMBER}."
            sh "docker push $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:${DOCKER_IMAGE_APPS}_production_${BUILD_NUMBER}"
            echo "Docker Image Build For Server Production Success"
          }
        }
      }
      stage('Docker Image Delete'){
        steps{
          if( env.GIT_BRANCH == 'master' ){
            sh "docker image rm -f $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:${DOCKER_IMAGE_APPS}_staging_${BUILD_NUMBER}"
            echo "Docker Image Delete For Server Stagging Success"
          }
          else if ( env.GIT_BRANCH == 'staging' ){
            sh "docker image rm -f $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:${DOCKER_IMAGE_APPS}_production_${BUILD_NUMBER}"
            echo "Docker Image Delete For Server Production Success"
          }
        }
      }
    }
}
