pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/naveengadde123/JMeterTesing.git'  // Your GitHub repository URL
        BRANCH = 'main'  // Your branch name
        CREDENTIALS_ID = '1d54e951-e865-4582-a541-e726548cfefd'  // Your credentials ID
        JMETER_HOME = 'C:/JMeter/apache-jmeter-5.6.3'  // Adjust this path to your JMeter installation
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning the Git repository...'
                // Cloning the specified branch from the repository and using the credentials
                git branch: "${BRANCH}", url: "${GIT_REPO}", credentialsId: "${CREDENTIALS_ID}"
            }
        }

        stage('Test JMeter') {
            steps {
                echo 'Running JMeter tests...'
                // Running JMeter tests with the .jmx file after cloning
                bat """
                    "${JMETER_HOME}/bin/jmeter.bat"
                """
            }
        }
    }
}
