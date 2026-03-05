pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'devahmedsamy/my-web-app'
        DOCKER_CREDS = 'dockerhub-token'
    }
    stages {
        stage('Build Image') {
            steps {
                script {
                    sh 'docker build -t $DOCKER_IMAGE:latest .'
                    sh 'docker tag $DOCKER_IMAGE:latest $DOCKER_IMAGE:v-$BUILD_NUMBER'
                }
            }
        }
        stage('Push to Registry') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: "$DOCKER_CREDS", passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                        sh 'echo $PASS | docker login -u $USER --password-stdin'
                        sh 'docker push $DOCKER_IMAGE:latest'
                        sh 'docker push $DOCKER_IMAGE:v-$BUILD_NUMBER'
                    }
                }
            }
        }
        stage('Deploy to K8s') {
            steps {
                script {
                    sh 'kubectl apply -f deployment.yaml' 
                    sh 'kubectl rollout restart deployment/my-web-app'
                }
            }
        }
    }
}
