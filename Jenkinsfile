pipeline {
    agent any
    environment {
        DOCKER_COMPOSE_FILE = "docker-compose.yml"
        VOTE_APP_URL = "http://localhost:8082"  // Updated port for vote service
    }
    stages {
        stage('Checkout Code') {
            steps {
                // Pull the latest code from your Git repository
                checkout scm
            }
        }
        stage('Build and Deploy') {
            steps {
                script {
                    // Bring up the application using docker-compose
                    sh "docker compose down"
                    sh "docker compose up -d"
                }
            }
        }
        stage('Test Vote Service') {
            steps {
                script {
                    // Wait for the vote service to become available
                    sh """
                    echo "Waiting for the vote service to be available..."
                    for i in {1..10}; do
                        curl -s $VOTE_APP_URL && break || sleep 5;
                    done
                    """
                }
            }
        }
        stage('Post-Deployment Validation') {
            steps {
                script {
                    // Check the health of the containers
                    sh "docker ps"
                    // Test the vote service
                    sh """
                    echo "Testing the vote service..."
                    curl -s $VOTE_APP_URL | grep -i "Vote"
                    """
                }
            }
        }
    }
    post {
        always {
            script {
                // Cleanup resources to avoid conflicts
                sh "docker compose down"
            }
        }
    }
}
