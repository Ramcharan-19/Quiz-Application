pipeline {
    agent any

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/Ramcharan-19/Quiz-Application.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build('quiz-app:latest')
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'charan195', passwordVariable: 'amma_1979')]) {
                    script {
                        docker.withRegistry('', 'dockerhub-creds') {
                            docker.image('quiz-app:latest').push('latest')
                        }
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
            }
        }
    }
}
