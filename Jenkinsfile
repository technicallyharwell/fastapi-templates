pipeline {
    options {
        buildDiscarder(logRotator(numToKeepStr: '10'))
        disableConcurrentBuilds()
        timeout(time: 30, unit: 'MINUTES')
        timestamps()
        ansiColor('xterm')
    }
    parameters {
        gitParameter branchFilter: 'origin/(.*)', name: 'BRANCH', type: 'PT_BRANCH'
    }
    environment {
        GIT_REPO_URL = 'https://github.com/technicallyharwell/fastapi-templates.git'
    }
    agent {
        dockerfile {
            filename 'api.Dockerfile'
            args '-u root:root'
        }
    }
    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out...'
                checkout scm
                sh 'which python'
                sh 'which pip'
            }
        }
        stage('Install') {
            steps {
                echo 'Installing...'
                sh """
                    pip install poetry==1.5.1
                    poetry install --no-interaction --no-cache
                    """
                echo 'Finished installing...'
            }
        }
        stage('Lint') {
            steps {
                sh """
                    echo "linting..."
                    python -m ruff .
                    echo "finished linting"
                    """
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
                sh """
                   chmod +x ./pretest.sh
                   ./pretest.sh
                   """
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}
