pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/naveengadde123/JMeterTesing.git'  // Your GitHub repository URL
        BRANCH = 'main'  // Your branch name
        CREDENTIALS_ID = '1d54e951-e865-4582-a541-e726548cfefd'  // Your credentials ID
        JMETER_HOME = 'C:/JMeter/apache-jmeter-5.6.3/apache-jmeter-5.6.3'  // Adjust this path to your JMeter installation
        RESULTS_DIR = 'C:/Training'  // Path for saving the result and reports
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning the Git repository...'
                git branch: "${BRANCH}", url: "${GIT_REPO}", credentialsId: "${CREDENTIALS_ID}"
            }
        }

        stage('Run JMeter Tests') {
            steps {
                echo 'Running JMeter tests...'
                // Run JMeter and generate HTML report
                bat """
                    "${JMETER_HOME}/bin/jmeter.bat" -n -t "${JMETER_HOME}/bin/Test.jmx" -l "${RESULTS_DIR}/results.jtl" -e -o "${RESULTS_DIR}/jmeter-report" -JDuration=1 -Jusers=3 -JCSVFilePath="${RESULTS_DIR}/input.csv"
                """
            }
        }

        stage('Publish JMeter Report') {
            steps {
                echo 'Publishing JMeter report...'
                // Use Jenkins HTML Publisher plugin to show JMeter HTML report
                publishHTML([
                    reportName: 'JMeter Test Report',
                    reportDir: "${RESULTS_DIR}/jmeter-report",
                    reportFiles: 'index.html',
                    keepAll: true
                ])
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
