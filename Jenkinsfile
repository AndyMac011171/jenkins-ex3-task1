pipeline {

    agent any

    stages {

         stage('Init') {
            steps {
                sh '''
                ssh -i ~/.ssh.id_rsa jenkins@10.154.0.34 << EOF
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

                sh 'docker build -t andymac011171/flask-jenk:latest -t andymac011171/flask-jenk:v${BUILD_NUMBER} .'
                sh 'docker build -t docker.io/andymac011171/nginx-jenk:latest -t docker.io/andymac011171/nginx-jenk:v${BUILD_NUMBER} ./nginx'

            }

        }
stage('Push') {

            steps {

                sh 'docker push andymac011171/flask-jenk:latest'
                sh 'docker push andymac011171/flask-jenk:v${BUILD_NUMBER}'
                sh 'docker push docker.io/andymac011171/nginx-jenk:latest'
                sh 'docker push docker.io/andymac011171/nginx-jenk:v${BUILD_NUMBER}'
            }

        }
        stage('Deploy') {

            steps {
                sh '''
                ssh -i ~/.ssh.id_rsa jenkins@10.154.0.34 << EOF
                docker run -d --name flask-app --network jenk-network docker.io/andymac011171/flask-jenk
                docker run -d -p 80:80 --name nginx --network jenk-network docker.io/andymac011171/nginx-jenk:latest
                '''

            }

        }

        stage('CleanUp') {
            steps {

                sh 'docker system prune -f'

            }
        }

    }

}