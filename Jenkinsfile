pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning the repository...'
                git branch: 'main',
                    credentialsId: '1d54e951-e865-4582-a541-e726548cfefd', // Your credentials ID here
                    url: 'https://github.com/naveengadde123/JMeterTesing.git' // Your GitHub repository URL
            }
        }

        stage('Test') {
            steps {
                echo 'Running JMeter tests...'
                // You can add the test execution script here
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Please check the logs.'
        }
    }
}
