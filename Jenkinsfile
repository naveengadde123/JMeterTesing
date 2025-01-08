pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/naveengadde123/JMeterTesing.git'
        BRANCH = 'main'
        CREDENTIALS_ID = '1d54e951-e865-4582-a541-e726548cfefd'
        JMETER_HOME = 'C:/JMeter/apache-jmeter-5.6.3/apache-jmeter-5.6.3'
        JMX_FILE = 'C:/JMeter/apache-jmeter-5.6.3/apache-jmeter-5.6.3/bin/Test.jmx'
        RESULTS_FILE = 'C:/Training/results.jtl'
        ERROR_LOG = 'C:/JMeter/apache-jmeter-5.6.3/apache-jmeter-5.6.3/bin/jmeter.log'
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
                    def results = readFile(file: "${RESULTS_FILE}").split('\\n')
                    def errorCount = 0
                    def errorDetails = []

                    results.each { line ->
                        if (line.contains('false')) {
                            errorCount++
                            def apiDetails = line.split(',')
                            errorDetails << "API: ${apiDetails[2]} | Reason: ${apiDetails[4]}"
                        }
                    }

                    if (errorCount > 0) {
                        writeFile(file: "${ERROR_LOG}", text: errorDetails.join("\n"))
                        error("Pipeline terminated due to ${errorCount} API failure(s):\n${errorDetails.join("\n")}")
                    }

                    echo 'All APIs passed successfully.'
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
        always {
            echo 'Pipeline execution completed.'
        }
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
