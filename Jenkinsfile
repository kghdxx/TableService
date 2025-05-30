pipeline {
    agent any

    environment {
        REPO_NAME = 'TableService'
        REPO_URL = 'https://github.com/kghdxx/TableService.git'
        DOCKER_COMPOSE_FILE = 'TableService/Tableservice/File Upload Service/data-lakehouse2.yml'
        APP_DIR = 'TableService/Tableservice/File Upload Service/app'
    }

    stages {

        stage('Clone Repo') {
            steps {
                sh "rm -rf ${REPO_NAME}"
                sh "git clone ${REPO_URL}"
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
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install pylint
                    venv/bin/pylint streamlitdw_fe.py || true
                    '''
                }
            }
        }

        stage('Security Scan (bandit)') {
            steps {
                dir("${APP_DIR}") {
                    sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install bandit
                    venv/bin/bandit -r . || true
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

    post {
        failure {
            echo " Pipeline failed."
        }
        success {
            echo " Pipeline completed successfully."
        }
    }
}
