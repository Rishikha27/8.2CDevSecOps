pipeline {
    agent any

    environment {
        // SONAR_TOKEN must match the ID of the Jenkins credential you created
        SONAR_TOKEN = credentials('SONAR_TOKEN')
    }

    stages {

        stage('Checkout SCM') {
            steps {
                // Checkout the GitHub repository
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Installing Node.js dependencies...'
                sh 'npm install'
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                echo 'Running SonarCloud scan...'
                sh 'sonar-scanner'
            }
        }

    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs for details.'
        }
    }
}
