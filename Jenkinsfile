pipeline {
    agent any 
    environment {
        DOCKERHUB_CREDENTIALS = credentials('test1')
    }
    stages {
        stage('Build docker image') {
            steps {  
                echo "Building Docker image..."
                sh 'docker build -t cerli18/flaskapp:$BUILD_NUMBER .'
            }
        }
        stage('login to dockerhub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'test1', usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
                    echo "Logging in to Docker Hub..."
                    sh "docker login -u cerli18 -p ilrec*2003"
                }
            }
        }
        stage('push image') {
            steps {
                echo "Pushing Docker image to Docker Hub..."
                sh 'docker push cerli18/flaskapp:$BUILD_NUMBER'
            }
        }
        stage('Deploy to Staging') {
            steps {
                // Pull the latest image from Docker Hub
                sh 'docker pull cerli18/flaskapp:$BUILD_NUMBER'

                // Stop and remove any existing containers
                sh 'docker stop myapp-container || true'
                sh 'docker rm myapp-container || true'

                // Run the new container with the latest image
                sh 'docker run -d --name myapp-container -p 8000:8000 cerli18/flaskapp:$BUILD_NUMBER'
            }
        }
    }
    post {
        always {
            echo "Logging out from Docker Hub..."
            sh 'docker logout'
        }
    }
}
