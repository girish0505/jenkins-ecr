pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = 'us-east-1'
        ECR_REPOSITORY = '799204179562.dkr.ecr.us-east-1.amazonaws.com/jenkins'
        DOCKER_IMAGE_TAG = 'latest' // You can change this to a specific tag or use a dynamic value
        DOCKERFILE_PATH = 'path/to/your/Dockerfile' // Relative path to the Dockerfile in your Jenkins workspace
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Docker Build & Push') {
            steps {
                script {
                    // Authenticate with AWS ECR
                    withCredentials([
			                  $class: 'AmazonWebServicesCredentialsBinding',
                        awsAccessKeyVariable(credentialsId: 'aws-jenkins', variable: 'AWS_ACCESS_KEY_ID'),
                        awsSecretKeyVariable(credentialsId: 'aws-jenkins', variable: 'AWS_SECRET_ACCESS_KEY')
                    ]) {
                        sh "aws ecr get-login-password --region $AWS_DEFAULT_REGION | docker login --username AWS --password-stdin $ECR_REPOSITORY"
                    }

                    // Build and tag the Docker image
                    sh "docker build -t $ECR_REPOSITORY:$DOCKER_IMAGE_TAG -f $DOCKERFILE_PATH ."

                    // Push the Docker image to ECR
                    sh "docker push $ECR_REPOSITORY:$DOCKER_IMAGE_TAG"
                }
            }
        }
    }
}
