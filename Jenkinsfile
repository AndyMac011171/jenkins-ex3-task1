pipeline {

    agent any

    stages {

         stage('Init') {
            steps {
                sh '''
                docker network create jenk-network || echo "Network Already Exists"
                docker stop flask-app || echo "flask-app Not Running"
                docker stop nginx || echo "nginx Not Running"
                docker rm flask-app || echo "flask-app Not Running"
                docker rm nginx || echo "nginx Not Running"
                '''
            }
        }

        stage('Build') {

            steps {

                sh 'docker build -t docker.io/AndyMac/flask-jenk:latest .'
                sh 'docker build -t docker.io/AndyMac/nginx-jenk:latest ./nginx'

            }

        }
stage('Push') {

            steps {

                sh 'docker push docker.io/AndyMac/flask-jenk:latest'
                sh 'docker push docker.io/AndyMac/nginx-jenk:latest'

            }

        }
        stage('Deploy') {

            steps {

                sh 'docker run -d --name flask-app --network jenk-network docker.io/AndyMac/flask-jenk'
                sh 'docker run -d -p 80:80 --name nginx --network jenk-network docker.io/AndyMac/nginx-jenk:latest'

            }

        }

        stage('CleanUp') {
            steps {

                sh 'docker system prune -f'

            }
        }

    }

}