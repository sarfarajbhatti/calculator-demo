pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/sarfarajbhatti/calculator-demo.git'
            }
        }

        stage('Build') {
            steps {
                sh '''
                python3 -m venv venv
                . venv/bin/activate
                pip install -r requirements.txt
                pip install pytest pytest-cov
                '''
            }
        }

        stage('Unit Test') {
            steps {
                sh '''
                . venv/bin/activate
                pytest --cov=app --cov-report=xml
                '''
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '''
                    . venv/bin/activate
                    sonar-scanner
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 10, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Email for Approval') {
            steps {
                mail to: 'your-email@gmail.com',
                     subject: "Approval Needed for Deployment",
                     body: """
Build #${env.BUILD_NUMBER} passed SonarQube Quality Gate.

Approve Deployment:
${env.BUILD_URL}

Open Jenkins and click Proceed.
"""
            }
        }

        stage('Manual Approval') {
            steps {
                timeout(time: 30, unit: 'MINUTES') {
                    input message: 'Approve deployment?',
                          ok: 'Deploy'
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying Calculator Application...'
            }
        }
    }
}