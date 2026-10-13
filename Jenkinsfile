pipeline {
    agent any

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out Retail Transaction Analytics Platform'
                checkout scm
            }
        }

        stage('Validate Files') {
            steps {
                sh '''
                    test -f Dockerfile.backend
                    test -f docker-compose.yml
                    test -f frontend/Dockerfile
                    test -f frontend/frontend-nginx.conf
                    echo "Required deployment files found"
                '''
            }
        }

        stage('Build Docker Images') {
            steps {
                sh 'sudo docker compose build'
            }
        }

        stage('Deploy Application') {
            steps {
                sh 'sudo docker compose up -d'
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                    sudo docker compose ps
                    curl --fail --retry 5 --retry-delay 3 \
                        http://localhost/health
                '''
            }
        }
    }

    post {
        success {
            echo 'Retail Analytics deployment completed successfully.'
        }
        failure {
            echo 'Pipeline failed. Review the stage logs.'
        }
    }
}
