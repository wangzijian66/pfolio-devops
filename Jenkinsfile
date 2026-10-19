pipeline {
    agent any
    stages{
        stage('检查项目文件'){
            steps{
                sh 'test -f index.html'
                sh 'test -f assets/css/style.css'
                sh 'test -f assets/js/script.js'
                sh 'test -f Dockerfile'
                sh 'test -f .dockerignore'
            }
        }

        stage('构建docker镜像'){
            steps{
                sh 'docker build -t shiter666/pfolio-frontend:latest .'
                sh 'docker images'
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_TOKEN'
                )]){
                    sh 'echo "$DOCKER_TOKEN" | docker login -u "$DOCKER_USER" --password-stdin'
                    sh 'docker push shiter666/pfolio-frontend:latest'
                    sh 'docker logout'
                }
            }
        }
    }
}