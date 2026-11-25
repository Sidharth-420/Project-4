
pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Environment Check') {
            steps {
                sh '''
                    echo "Checking installed tools..."
                    git --version
                    python3 --version
                    semgrep --version
                    trivy --version
                '''
            }
        }

        stage('Python Syntax Check') {
            steps {
                sh '''
                    echo "Checking Python files..."
                    python3 -m compileall -q project4/project4
                '''
            }
        }

        stage('Semgrep SAST Scan') {
            steps {
                sh '''
                    semgrep scan --config p/django \
                    --exclude project4/project4/static \
                    --exclude project4/project4/media .
                '''
            }
        }

        stage('Trivy Security Scan') {
            steps {
                sh '''
                    trivy fs --scanners vuln,misconfig,secret .
                '''
            }
        }
    }

    post {
        success {
            echo 'DevSecOps pipeline completed!'
        }

        failure {
            echo 'Pipeline failed. Check the console output.'
        }
    }
}
