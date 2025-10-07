pipeline {
    agent any

    environment {
        IMAGE_NAME = "apoorvar12/django-notes-app"
        IMAGE_TAG = "${BUILD_NUMBER}"
        FULL_IMAGE = "${IMAGE_NAME}:${IMAGE_TAG}"
        CONTAINER_NAME = "django_notes_app_test"
    }

    stages {
        stage('Checkout') {
            steps {
                echo "Checking out source code..."
                git branch: 'main', url: 'https://github.com/apoorvarameshac/django-notes-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image"
                sh 'docker build -t ${FULL_IMAGE} .'
            }
        }
        stage('Push to DockerHub') {
            steps {
                echo "Pushing image to DockerHub..."
                  withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                  sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push $FULL_IMAGE
                    '''
                 }
            }
        }

        stage('Run Containersss') {
            steps {
                echo "Running container"
                sh '''
                docker stop ${CONTAINER_NAME} || true
                docker rm ${CONTAINER_NAME} || true
                docker run -d -p 8000:8000 --name ${CONTAINER_NAME} ${FULL_IMAGE} 

                '''
                
            }
        }
        stage('Deploy container') {
            steps {
                sh '''
                kubectl delete --all pods
                kubectl apply -f manifest/deployment.yaml
                kubectl apply -f manifest/service.yaml

                '''
            }
        }
      }
    }

