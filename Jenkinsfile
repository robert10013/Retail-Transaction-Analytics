pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Source code checked out'
            }
        }

        stage('Build') {
            steps {
                sh 'docker build -t retail-analytics:latest .'
            }
        }

        stage('Test') {
            steps {
                sh 'python3 -m py_compile app.py'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker rm -f retail-analytics || true

                    docker run -d \
                      --name retail-analytics \
                      --restart unless-stopped \
                      -p 3000:8000 \
                      retail-analytics:latest

                    sleep 10

                    curl --retry 5 --retry-delay 2 \
                      --retry-connrefused \
                      -f http://localhost:3000/
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment successful'
        }
        failure {
            echo 'Deployment failed. Check Console Output.'
        }
    }
}
