pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/naveengadde123/JMeterTesing.git'  // Your GitHub repository URL
        BRANCH = 'main'  // Your branch name
        CREDENTIALS_ID = '1d54e951-e865-4582-a541-e726548cfefd'  // Your credentials ID
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning the Git repository...'
                // Cloning the specified branch from the repository and using the credentials
                git branch: "${BRANCH}", url: "${GIT_REPO}", credentialsId: "${CREDENTIALS_ID}"
            }
        }
    }
}
