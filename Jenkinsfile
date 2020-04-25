pipeline {
  agent any 
  triggers {
        pollSCM(env.GIT_BRANCH == 'master' ? '* * * * *' : env.GIT_BRANCH == 'staging' ? '* * * * *' : '')
    }
  stages {
      stage('Checkout SCM') {
        steps{
          checkout scm
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
          script {
            if ( env.GIT_BRANCH == 'staging' ){
              sh "docker image build . -t $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:${DOCKER_IMAGE_APPS}_staging_${BUILD_NUMBER}"
              sh "docker push $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:${DOCKER_IMAGE_APPS}_staging_${BUILD_NUMBER}"
              echo "Docker Image ${BUILD_NUMBER} Build For Server Stagging ${currentBuild.currentResult}"
            }  
            else if ( env.GIT_BRANCH == 'master' ){
              sh "docker image build . -t $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:${DOCKER_IMAGE_APPS}_production_${BUILD_NUMBER}"
              sh "docker push $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:${DOCKER_IMAGE_APPS}_production_${BUILD_NUMBER}"
              echo "Docker Image ${BUILD_NUMBER} Build For Server Production ${currentBuild.currentResult}"
            }
          }  
        }
      }
      stage('Docker Image Delete'){
        steps{
          script {
            if ( env.GIT_BRANCH == 'staging' ){
              sh "docker image rm -f $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:${DOCKER_IMAGE_APPS}_staging_${BUILD_NUMBER}"
              echo "Docker Image ${BUILD_NUMBER} Delete For Server Stagging ${currentBuild.currentResult}"
            }
            else if ( env.GIT_BRANCH == 'master' ){
              sh "docker image rm -f $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:${DOCKER_IMAGE_APPS}_production_${BUILD_NUMBER}"
              echo "Docker Image ${BUILD_NUMBER} Delete For Server Production ${currentBuild.currentResult}"
            }
          }  
        }
      }
      stage('Deploy TO K8S'){
        steps{
          script {
            if ( env.GIT_BRANCH == 'staging' ){
              sh 'wget https://raw.githubusercontent.com/ikhsannugs/big-project/master/landpage-staging-deploy.yaml'
              sh 'sed -i "s/versi/$BUILD_NUMBER/g" "${DOCKER_IMAGE_APPS}"-staging-deploy.yaml'
              sh 'kubectl apply -f "${DOCKER_IMAGE_APPS}"-staging-deploy.yaml'
              sh 'rm -rf *'
              echo "Deploy ${BUILD_NUMBER} To Server Staging ${currentBuild.currentResult}"
            }
            else if ( env.GIT_BRANCH == 'master' ){
              sh 'wget https://raw.githubusercontent.com/ikhsannugs/big-project/master/landpage-production-deploy.yaml'
              sh 'sed -i "s/versi/$BUILD_NUMBER/g" "${DOCKER_IMAGE_APPS}"-production-deploy.yaml'
              sh 'kubectl apply -f "${DOCKER_IMAGE_APPS}"-production-deploy.yaml'
              sh 'rm -rf *'
              echo "Deploy ${BUILD_NUMBER} To Server Production ${currentBuild.currentResult}"
            }
          }  
        }
      }
    }
  post {
        always {
          script {
            if ( env.GIT_BRANCH == 'staging' ){
              echo "DEPLOY ${DOCKER_IMAGE_APPS} NUMBER ${BUILD_NUMBER} TO SERVER STAGING ${currentBuild.currentResult}"
              slackSend message: "DEPLOY ${DOCKER_IMAGE_APPS} NUMBER ${BUILD_NUMBER} TO SERVER STAGING ${currentBuild.currentResult}"

            }
            else if ( env.GIT_BRANCH == 'master' ){
              echo "DEPLOY ${DOCKER_IMAGE_APPS} NUMBER ${BUILD_NUMBER} TO SERVER STAGING ${currentBuild.currentResult}"
              slackSend message: "DEPLOY ${DOCKER_IMAGE_APPS} NUMBER ${BUILD_NUMBER} TO SERVER PRODUCTION ${currentBuild.currentResult}"
            }
          }  
        }
  }  
}

