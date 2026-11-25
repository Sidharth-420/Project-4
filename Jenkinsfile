
pipeline {
    agent any

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Environment Check') {
            steps {
                sh '''
                    git --version
                    python3 --version
                    java -version
                '''
            }
        }

        stage('Python Syntax Check') {
            steps {
                sh '''
                    python3 -m py_compile project4/project4/manage.py
                    python3 -m compileall -q project4/project4/project4
                '''
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'sonar-scanner'

                    withSonarQubeEnv('SonarQube') {
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                            -Dproject.settings=sonar-project.properties
                        """
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Project-4 pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed. Check Console Output.'
        }
    }
}
