pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
    }

    environment {
        DOCKER_IMAGE = 'faith901/jenkins_server'
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials'
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Checking out repository..."
                cleanWs()
                git url: 'https://github.com/byiringirofabrice/jenkins_server.git', branch: 'main'
            }
        }

        stage('Build') {
            steps {
                echo "Listing project files..."
                sh 'ls -la'
            }
        }

        stage('Test') {
            steps {
                echo "Running tests (placeholder)..."
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.withServer('tcp://localhost:2375') {
                        def dockerImage = docker.build("${DOCKER_IMAGE}:latest", "--no-cache .")
                        docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                            dockerImage.push('latest')
                        }
                    }
                }
            }
        }

        stage('Deploy to Local Docker Host') {
            steps {
                sh '''
                    docker stop jenkins_server || true
                    docker rm -f jenkins_server || true
                    docker run -d --name jenkins_server -p 8090:3000 faith901/jenkins_server:latest
                '''
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed. Check logs."
        }
    }
}
