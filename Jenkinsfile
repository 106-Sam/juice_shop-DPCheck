pipeline {
    agent any
        tools {
        nodejs 'node26'
    }
    stages {
        stage('Verify NodeJS') {
            steps {
                sh 'node -v'
                sh 'npm -v'
            }
        }
        stage('git checkout') {
            steps {
                echo 'Cloning juice-shop source code'
                git 'https://github.com/juice-shop/juice-shop.git'
                sh 'pwd'
                sh 'npm install > juice_shop_installation.log 2>&1'
            }
        }
        stage('run Juice Shop server'){
            steps{
                echo 'Starting the server...'
                sh 'nohup npm start &'
                sh 'sleep 20'
            }
        }
        stage('OWASP Dependency Check'){
            steps{
                withCredentials([string(credentialsId: 'nvd-api-key', variable: 'NVD_API_KEY')]) {
                    dependencyCheck(
                        odcInstallation: 'dependency-check',
                        additionalArguments: '--scan . --format HTML --format XML --nvdApiKey ${NVD_API_KEY}' 
                    )
                }
            }
        }
        stage('DP-Report'){
            steps{
                dependencyCheckPublisher pattern: 'dependency-check-report.xml'
            }
        }
    }
}
