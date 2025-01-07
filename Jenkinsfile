pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/naveengadde123/JMeterTesing.git'  // Your GitHub repository URL
        BRANCH = 'main'  // Your branch name
        JMETER_HOME = 'C:/JMeter/apache-jmeter-5.6.3'  // Adjust path to your JMeter installation if necessary
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning the Git repository...'
                git branch: "${BRANCH}", url: "${GIT_REPO}"  // Cloning the specified branch from the repository
            }
        }

        stage('Build') {
            steps {
                echo 'Building the project...'
                // Add your build commands here, e.g., for Maven, Gradle, npm, etc.
                // Example for Maven:
                sh 'mvn clean install'
            }
        }

        stage('Test') {
            steps {
                echo 'Running JMeter tests...'
                // Running JMeter tests with your Test.jmx file
                bat """
                    "${JMETER_HOME}/bin/jmeter.bat" -n -t "${WORKSPACE}/Test.jmx" -l "${WORKSPACE}/results.jtl" -JDuration=1 -Jusers=3 -JCSVFilePath="${WORKSPACE}/input.csv"
                """
            }
        }

        stage('Publish Report') {
            steps {
                echo 'Publishing JMeter performance report...'
                // Publish the performance report based on the .jtl file
                perfReport filterRegex: '', sourceDataFiles: '**/*.jtl'
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
