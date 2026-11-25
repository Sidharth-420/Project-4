
pipeline {
    agent any

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    environment {
        PROJECT_DIR = 'project4/project4'
    }

    stages {

        // Stage 1: Checkout source code
        stage('Checkout') {
            steps {
                echo 'Checking out Project-4 from GitHub...'
                checkout scm
            }
        }

        // Stage 2: Verify installed tools
        stage('Environment Check') {
            steps {
                sh '''
                    echo "===== ENVIRONMENT CHECK ====="

                    echo "Git:"
                    git --version

                    echo "Python:"
                    python3 --version

                    echo "Semgrep:"
                    semgrep --version

                    echo "Trivy:"
                    trivy --version
                '''
            }
        }

        // Stage 3: Python syntax validation
        stage('Python Syntax Check') {
            steps {
                sh '''
                    echo "===== PYTHON SYNTAX CHECK ====="

                    python3 -m py_compile \
                        project4/project4/manage.py

                    python3 -m compileall -q \
                        project4/project4/project4

                    echo "Python syntax validation completed."
                '''
            }
        }

        // Stage 4: Django SAST using Semgrep
        stage('Semgrep SAST Scan') {
            steps {
                sh '''
                    echo "===== SEMGREP SECURITY SCAN ====="

                    semgrep scan \
                        --config p/django \
                        --exclude project4/project4/static \
                        --exclude project4/project4/media \
                        --exclude '*.sql' \
                        --exclude 'Jenkinsfile' \
                        --json \
                        --output semgrep-report.json \
                        .
                '''
            }
        }

        // Stage 5: Trivy security scanning
        stage('Trivy Security Scan') {
            steps {
                sh '''
                    echo "===== TRIVY SECURITY SCAN ====="

                    trivy fs \
                        --scanners vuln,misconfig,secret \
                        --skip-dirs project4/project4/static \
                        --skip-dirs project4/project4/media \
                        --skip-files '*.sql' \
                        --exit-code 0 \
                        --format json \
                        --output trivy-report.json \
                        .

                    echo "Trivy scan completed."
                '''
            }
        }

        // Stage 6: Archive security reports
        stage('Archive Security Reports') {
            steps {
                archiveArtifacts artifacts: 'semgrep-report.json,trivy-report.json',
                    allowEmptyArchive: false
            }
        }
    }

    post {
        success {
            echo '''
            =====================================
             PROJECT-4 DEVSECOPS PIPELINE PASSED
            =====================================
            Checkout: SUCCESS
            Python Syntax: SUCCESS
            Semgrep: COMPLETED
            Trivy: COMPLETED
            Security Reports: ARCHIVED
            =====================================
            '''
        }

        failure {
            echo '''
            =====================================
             PROJECT-4 DEVSECOPS PIPELINE FAILED
            =====================================
            Check the Jenkins Console Output.
            =====================================
            '''
        }

        always {
            echo 'Project-4 pipeline execution finished.'
        }
    }
}
