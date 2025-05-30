pipeline {
    agent any

    environment {
        // Updated paths to match actual directory structure
        DOCKER_COMPOSE_FILE = 'TableService/Tableservice/File Upload Service/data-lakehouse2.yml'
        APP_DIR = 'TableService/Tableservice/File Upload Service/app'
    }

    stages {

        stage('Clone Repo') {
            steps {
                // Clean up any existing repo to prevent re-clone issues
                sh 'rm -rf TableService'
                sh 'git clone https://github.com/kghdxx/TableService.git'
            }
        }

        stage('Build') {
            steps {
                sh "docker-compose -f '${DOCKER_COMPOSE_FILE}' build"
            }
        }

        stage('Lint (pylint)') {
            steps {
                dir("${APP_DIR}") {
                    sh '''
                    pip install pylint
                    pylint streamlitdw_fe.py || true
                    '''
                }
            }
        }

        stage('Security Scan (bandit)') {
            steps {
                dir("${APP_DIR}") {
                    sh '''
                    pip install bandit
                    bandit -r . || true
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                sh "docker-compose -f '${DOCKER_COMPOSE_FILE}' up -d"
            }
        }
    }
}
