pipeline {
    agent any

    environment {
        CX_PROJECT_NAME = 'juice-shop'
        DAST_TARGET_URL = 'http://your-deployed-juice-shop-url.com' // Set after deploy
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Checkmarx SAST Scan') {
            steps {
                script {
                    checkmarxOneScan(
                        projectName: env.CX_PROJECT_NAME,
                        credentialsId: 'checkmarx-one-token',
                        additionalParams: '--report-format html --output-path checkmarx_sast_report.html'
                    )
                }
            }
            post {
                always {
                    archiveArtifacts artifacts: 'checkmarx_sast_report.html', allowEmptyArchive: true
                }
            }
        }

        stage('Static Code Quality Checks') {
            steps {
                sh '''
                npm install
                npm install -g eslint stylelint prettier solhint pug-lint

                npx eslint "**/*.{js,ts}" || true
                npx stylelint "**/*.scss" || true
                npx prettier --check "**/*.{js,ts,html,scss,pug}" || true
                npx solhint "**/*.sol" || true
                npx pug-lint "**/*.pug" || true
                '''
            }
        }

        stage('Build Juice Shop') {
            steps {
                sh '''
                npm ci
                npm run build
                '''
            }
        }

        stage('Deploy to AWS (Placeholder)') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-credentials-id']]) {
                    sh '''
                    echo "Deployment script placeholder"
                    echo "Use AWS CLI, Beanstalk, or SCP to deploy to EC2 or S3."
                    '''
                }
            }
        }

        stage('Checkmarx DAST Scan') {
            steps {
                script {
                    checkmarxOneScan(
                        projectName: "${CX_PROJECT_NAME}-DAST",
                        credentialsId: 'checkmarx-one-token',
                        additionalParams: "--scan-type DAST --url ${DAST_TARGET_URL} --report-format html --output-path checkmarx_dast_report.html"
                    )
                }
            }
            post {
                always {
                    archiveArtifacts artifacts: 'checkmarx_dast_report.html', allowEmptyArchive: true
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed.'
        }
    }
}
