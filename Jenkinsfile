pipeline {
    agent any
    stages {
        stage('Build Backend Image') {
            steps {
                sh '''
                    docker rmi -f backend-app || true
                    docker build -t backend-app backend
                '''
            }
        }
        stage('Deploy Backend Containers') {
            steps {
                sh '''
                    docker rm -f backend1 backend2 nginx-lb || true
                    docker network rm app-network || true
                    docker network create app-network
                    docker run -d --name backend1 --network app-network backend-app
                    docker run -d --name backend2 --network app-network backend-app
                '''
            }
        }
        stage('Deploy NGINX Load Balancer') {
            steps {
                sh '''
                    docker run -d --name nginx-lb --network app-network -p 80:80 \
                        -v $(pwd)/nginx/default.conf:/etc/nginx/conf.d/default.conf \
                        nginx
                '''
            }
        }
    }
    post {
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed. Check console logs for errors.'
        }
    }
}
