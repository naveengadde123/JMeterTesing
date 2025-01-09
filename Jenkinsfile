pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/naveengadde123/JMeterTesing.git'
        BRANCH = 'main'
        CREDENTIALS_ID = '1d54e951-e865-4582-a541-e726548cfefd'
        JMETER_HOME = 'C:/JMeter/apache-jmeter-5.6.3/apache-jmeter-5.6.3'
        JMX_FILE = "${JMETER_HOME}/bin/Test.jmx"
        RESULTS_FILE = 'C:/Training/results.jtl'
        ERROR_LOG = "${JMETER_HOME}/bin/jmeter_error.log"
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
                script {
                    def result = bat returnStatus: true, script: """
                        "${JMETER_HOME}/bin/jmeter.bat" -n -t "${JMX_FILE}" -l "${RESULTS_FILE}" -JDuration=1 -Jusers=3
                    """
                    if (result != 0) {
                        error "JMeter execution failed with exit code: ${result}"
                    }
                }
            }
        }

        stage('Check Test Results') {
            steps {
                echo 'Checking test results for failures...'
                script {
                    def results = readFile(file: "${RESULTS_FILE}").split('\\n')
                    def errorFound = false

                    for (line in results) {
                        if (line.contains('false')) {
                            def apiDetails = line.split(',')
                            def errorDetails = "API: ${apiDetails[2]} | Reason: ${apiDetails[4]}"
                            writeFile(file: "${ERROR_LOG}", text: errorDetails)
                            errorFound = true
                            error("Pipeline terminated due to API failure:\\n${errorDetails}")
                        }
                    }

                    if (!errorFound) {
                        echo 'All APIs passed successfully.'
                    }
                }
            }
        }

        stage('Publish Report') {
            steps {
                echo 'Publishing performance report...'
                perfReport filterRegex: '', sourceDataFiles: "${RESULTS_FILE}", graphType: 'jtl'
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
                    echo "Error details:\\n" + readFile(file: "${ERROR_LOG}")
                }
            }
        }
    }
}
