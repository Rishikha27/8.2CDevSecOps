pipeline {
    agent any

    environment {
        // Load SonarCloud token from Jenkins credentials
        SONAR_TOKEN = credentials('SONAR_TOKEN')
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out code from GitHub'
                git branch: 'main',
                    url: 'https://github.com/Rishikha27/8.2CDevSecOps.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Installing Node.js dependencies'
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                echo 'Running unit tests'
                sh 'npm test'
            }
        }

        stage('Generate Coverage Report') {
            steps {
                echo 'Generating coverage report'
                sh 'npm run coverage'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                echo 'Running NPM audit for vulnerabilities'
                sh 'npm audit'
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                echo 'Starting SonarCloud analysis...'
                sh '''
                # Download SonarScanner CLI (macOS-friendly)
                curl -L -o sonar-scanner-cli.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.14.0.42296-macosx.zip
                
                # Unzip the scanner
                unzip -o sonar-scanner-cli.zip

                # Run SonarScanner
                ./sonar-scanner-5.14.0.42296-macosx/bin/sonar-scanner \
                    -Dsonar.projectKey=<your_project_key> \
                    -Dsonar.organization=<your_organization_name> \
                    -Dsonar.sources=. \
                    -Dsonar.host.url=https://sonarcloud.io \
                    -Dsonar.login=$SONAR_TOKEN \
                    -Dsonar.exclusions=node_modules/**,test/** \
                    -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info
                '''
            }
        }

        stage('Build Application') {
            steps {
                echo 'Building Node.js application'
                sh 'npm run build'
            }
        }

        stage('Archive Build Artifacts') {
            steps {
                echo 'Archiving build artifacts'
                archiveArtifacts artifacts: '**/dist/**', allowEmptyArchive: true
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished. Check console output for details.'
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs for errors.'
        }
    }
}
