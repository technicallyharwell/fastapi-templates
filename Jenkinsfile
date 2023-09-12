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
            args '-u root:root -v /var/lib/jenkins:/var/lib/jenkins -v /usr/bin/java:/usr/bin/java'
        }
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Install') {
        // install dependencies used throughout the pipeline
            steps {
                sh """
                    poetry lock
                    poetry install
                    """
            }
        }
        stage('Lint') {
            steps {
                sh """
                    echo "linting..."
                    poetry run ruff .
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
        stage('Code Coverage') {
            environment {
                SCANNER_HOME = tool 'SonarQubeScanner'
            }
            steps {
                withEnv(["PATH=$SCANNER_HOME/bin:$PATH"]) {
                    withSonarQubeEnv('SonarQube') {
                        sh "${SCANNER_HOME}/bin/sonar-scanner"
                    }
                }
            }
        }
    }
}