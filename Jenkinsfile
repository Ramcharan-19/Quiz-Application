pipeline {
    agent any

    environment {
        GIT_COMMIT = ''
    }

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/Ramcharan-19/Quiz-Application.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    env.GIT_COMMIT = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
                    docker.build("quiz-app:${env.GIT_COMMIT}")
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    script {
                        sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                        docker.withRegistry('', 'dockerhub-creds') {
                            docker.image("quiz-app:${env.GIT_COMMIT}").push('latest')
                            docker.image("quiz-app:${env.GIT_COMMIT}").push(env.GIT_COMMIT)
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
