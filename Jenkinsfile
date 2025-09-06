pipeline {
  agent any

  environment {
    AWS_DEFAULT_REGION = 'us-east-1'
    ECR_URI = '867064025954.dkr.ecr.us-east-1.amazonaws.com/springboot-app'
    IMAGE_TAG = 'latest'
    CONTAINER_NAME = 'springboot-app'
    HOST_PORT = '80'
    CONTAINER_PORT = '8080'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build JAR') {
      steps {
        sh 'mvn -q -DskipTests package'
      }
    }

    stage('Build & Push Docker Image') {
      steps {
        sh '''
          aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${ECR_URI}
          docker build -t ${ECR_URI}:${IMAGE_TAG} .
          docker push ${ECR_URI}:${IMAGE_TAG}
        '''
      }
    }

    stage('Deploy Container on Host') {
      steps {
        sh '''
          docker rm -f ${CONTAINER_NAME} || true
          docker pull ${ECR_URI}:${IMAGE_TAG}
          docker run -d --name ${CONTAINER_NAME} -p ${HOST_PORT}:${CONTAINER_PORT} ${ECR_URI}:${IMAGE_TAG}
        '''
      }
    }
  }
}
