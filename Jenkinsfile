
pipeline {
    agent any

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    environment {
        APP_DIR = '/home/ec2-user/Retail-Transaction-Analytics-Platform'
        COMPOSE_FILE = '/home/ec2-user/Retail-Transaction-Analytics-Platform/docker-compose.yml'
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
                    test -f "$COMPOSE_FILE"
                    test -f "$APP_DIR/.env"
                    test -f "$APP_DIR/online_retail_II.xlsx"
                    test -f "$APP_DIR/Dockerfile.backend"
                    test -f "$APP_DIR/frontend/Dockerfile"
                    test -f "$APP_DIR/frontend/frontend-nginx.conf"

                    echo "Required deployment files found"
                '''
            }
        }

        stage('Build Docker Images') {
            steps {
                sh '''
                    docker compose -f "$COMPOSE_FILE" build
                '''
            }
        }

        stage('Deploy Application') {
            steps {
                sh '''
                    docker compose -f "$COMPOSE_FILE" up -d
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                    docker compose -f "$COMPOSE_FILE" ps

                    curl --fail --retry 5 --retry-delay 3 \
                        http://localhost/health

                    echo "Application health check passed"
                '''
            }
        }
    }

    post {
        success {
            echo 'Retail Transaction Analytics deployment succeeded.'
        }

        failure {
            echo 'Pipeline failed. Review the failed stage in Console Output.'
        }
    }
}

