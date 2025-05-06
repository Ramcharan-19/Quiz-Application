pipeline {
    agent any

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/Ramcharan-19/Quiz-Application.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def gitCommit = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
                    docker.build("quiz-app:${gitCommit}")
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'charan195', passwordVariable: 'amma_1979')]) {
                    script {
                        docker.withRegistry('', 'dockerhub-creds') {
                            docker.image("quiz-app:${gitCommit}").push('latest')
                            docker.image("quiz-app:${gitCommit}").push(gitCommit)
                        }
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    sh 'kubectl apply -f deployment.yaml'
                }
            }
        }
    }
}
