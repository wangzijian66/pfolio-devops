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
                sh 'test -f k8s/deployment.yaml'
                sh 'test -f k8s/service.yaml'
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

       stage('部署到kubernetes'){
          steps{
            sh 'mkdir -p .tools'
            sh 'curl -fSL --retry 2 "https://dl.k8s.io/release/v1.37.0/bin/linux/amd64/kubectl" -o .tools/kubectl'
            sh 'chmod +x .tools/kubectl'
            sh '.tools/kubectl version --client'

            withKubeConfig([
              credentialsId: 'docker-desktop-kubeconfig',
              contextName: 'docker-desktop'
              ]) {
              sh '.tools/kubectl config current-context'
              sh '.tools/kubectl get nodes'
              sh '.tools/kubectl apply -f k8s/deployment.yaml'
              sh '.tools/kubectl apply -f k8s/service.yaml'
              sh '.tools/kubectl rollout status deployment/pfolio --timeout=120s'
            }
          }
        }
    }
    post {
    always {
        sh 'rm -f .tools/kubectl'
      }
    }
}