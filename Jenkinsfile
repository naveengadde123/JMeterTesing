pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/naveengadde123/JMeterTesing.git'  // Your GitHub repository URL
        BRANCH = 'main'  // Your branch name
        CREDENTIALS_ID = '1d54e951-e865-4582-a541-e726548cfefd'  // Your credentials ID
        JMETER_HOME = 'C:/JMeter/apache-jmeter-5.6.3/apache-jmeter-5.6.3'  // Path to JMeter installation
        JMX_FILE = 'C:/JMeter/apache-jmeter-5.6.3/apache-jmeter-5.6.3/bin/Test.jmx'  // Path to the .jmx file
        RESULTS_FILE = 'C:/Training/results.jtl'  // Path to store the results
        ERROR_LOG = 'C:/JMeter/apache-jmeter-5.6.3/apache-jmeter-5.6.3/bin/jmeter.log'  // Path to store error details
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
                bat """
                    "${JMETER_HOME}/bin/jmeter.bat" -n -t "${JMX_FILE}" -l "${RESULTS_FILE}" -JDuration=1 -Jusers=3
                """
            }
        }

        stage('Check Test Results') {
            steps {
                echo 'Checking test results for failures...'
                script {
                    def failed = false
                    def failedAPIs = []

                    def results = readFile(file: "${RESULTS_FILE}").split('\n')
                    for (line in results) {
                        if (line.contains('false')) {  // Assuming 'false' in the line indicates a failure
                            failed = true
                            def apiDetails = line.split(',')  // Adjust split logic based on JMeter result file format
                            failedAPIs.add("API: ${apiDetails[2]} | Reason: ${apiDetails[4]}") // Update index based on file
                        }
                    }

                    if (failed) {
                        writeFile(file: "${ERROR_LOG}", text: failedAPIs.join('\n'))
                        error("One or more APIs failed. Details logged to ${ERROR_LOG}.")
                    } else {
                        echo 'All APIs passed successfully.'
                    }
                }
            }
        }

        stage('Publish Report') {
            steps {
                echo 'Publishing performance report...'
                perfReport filterRegex: '', sourceDataFiles: "${RESULTS_FILE}"
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Please check the logs.'
            script {
                if (fileExists("${ERROR_LOG}")) {
                    echo "Error details:\n" + readFile(file: "${ERROR_LOG}")
                }
            }
        }
    }
}
