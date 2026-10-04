pipeline {
    agent any

    stages {
                stage('Checkout') {
            steps {
                git url: 'https://github.com/kaijiharris/cloud-bootcamp.git', branch: 'main'
            }
        }
        stage('Build') {
            steps {
                dir('week6-docker') {
                    sh 'docker build -t flask-app:${BUILD_NUMBER} -t flask-app:latest .'
                }
            }
        }

        stage('Test') {
            steps {
                sh '''
                  docker run --rm flask-app:${BUILD_NUMBER} python -c "from app import app; r = app.test_client().get('/'); assert r.status_code == 200; assert b'Hello from Dockerized Flask App!' in r.data; print('TEST PASSED:', r.data.decode())"
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker rm -f flask-app || true'
                sh 'docker run -d --name flask-app -p 80:5000 flask-app:${BUILD_NUMBER}'
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}