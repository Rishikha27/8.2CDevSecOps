pipeline {
    agent any

    environment {
        SONAR_TOKEN = credentials('SONAR_TOKEN')
    }

    tools {
        nodejs 'nodejs'  // Make sure this matches your NodeJS tool name in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Rishikha27/8.2CDevSecOps.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test || true' // continue even if tests fail
            }
        }

        stage('Generate Coverage Report') {
            steps {
                sh 'npm run coverage || true'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                sh 'npm audit --json || true'
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                sh '''
                    wget https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.14.0.42296-macosx.zip
                    unzip -o sonar-scanner-cli-5.14.0.42296-macosx.zip
                    ./sonar-scanner-5.14.0.42296-macosx/bin/sonar-scanner \
                        -Dsonar.projectKey=<your_project_key> \
                        -Dsonar.organization=<your_organization_name> \
                        -Dsonar.sources=. \
                        -Dsonar.host.url=https://sonarcloud.io \
                        -Dsonar.login=$SONAR_TOKEN \
                        -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info
                '''
            }
        }

        stage('Build Application') {
            steps {
                sh 'npm run build || true'
            }
        }

        stage('Archive Build Artifacts') {
            steps {
                archiveArtifacts artifacts: '**/dist/**', allowEmptyArchive: true
            }
        }
    }

    post {
        always {
            echo 'Pipeline finishe
