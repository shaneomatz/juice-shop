pipeline {
    agent any

    tools {
       nodejs "nodejs 14.17.0"
    }

    environment {
        SNYK_TOKEN = credentials('SNYK_TOKEN')
    }
    stages {
        stage('Download Latest Snyk CLI') {
            steps {
                script {

                    snyk_cli_dl_linux="https://static.snyk.io/cli/latest/snyk-linux-arm64"
                    echo "Download URL: ${snyk_cli_dl_linux}"

                    sh """
                        curl -Lo ./snyk "${snyk_cli_dl_linux}"
                        chmod +x ./snyk
                        mkdir -p reports
                        ls -la
                        ./snyk -v
                        ./snyk auth $SNYK_TOKEN
                    """
                }
            }
        }
        stage('Get npm and snyk-to-html'){
            steps {
                script {
                    sh """
                        npm install
                        npm install snyk-to-html -g
                    """
                }
            }
        }
        stage('Test Snyk') {
            steps {
                script {
                    sh "./snyk test --json | snyk-to-html -o reports/results.html"
                }
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
    post {
        always {
            publishHTML (target : [allowMissing: false,
             alwaysLinkToLastBuild: true,
             keepAll: true,
             reportDir: 'reports',
             reportFiles: 'results.html',
             reportName: 'snyk_test_report',
             reportTitles: 'snyk_test'])
        }
    }
}
