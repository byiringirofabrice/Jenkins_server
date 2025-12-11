pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'faith901/jenkins_server'
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials'
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Cloning the repository if it doesn't exist..."
                sh '''
                if [ ! -d ".git" ]; then
                    git clone https://github.com/byiringirofabrice/jenkins_server.git .
                else
                    echo "Repository already exists. Skipping clone."
                    git fetch --all
                    git reset --hard origin/main
                fi
                '''
            }
        }

        stage('Build') {
            steps {
                echo "Building the project..."
                sh 'ls -la'
            }
        }

        stage('Test') {
            steps {
                echo "Running tests..."
                // Add test commands here later
            }
        }

        stage('Build and Push Docker Image') {
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
                    docker stop jenkins_server || echo "Container not running..."
                    docker rm -f jenkins_server || echo "No container to remove..."
                    docker run -d --name jenkins_server -p 8090:3000 ${DOCKER_IMAGE}:latest
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
