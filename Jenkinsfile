pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/naveengadde123/JMeterTesing.git'
        BRANCH = 'main'
        CREDENTIALS_ID = '1d54e951-e865-4582-a541-e726548cfefd'
        JMETER_HOME = 'C:/JMeter/apache-jmeter-5.6.3/apache-jmeter-5.6.3'
        JMX_FILE = 'C:/JMeter/apache-jmeter-5.6.3/apache-jmeter-5.6.3/bin/Test.jmx'
        RESULTS_FILE = 'C:/Training/results.jtl'
        MAX_RESPONSE_TIME_MS = 30000
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: "${BRANCH}", url: "${GIT_REPO}", credentialsId: "${CREDENTIALS_ID}"
                echo 'Cloning the repository...'
            }
        }

        stage('Run JMeter Tests') {
            steps {
                bat """
                    "${JMETER_HOME}/bin/jmeter.bat" -n -t "${JMX_FILE}" -l "${RESULTS_FILE}" -f
                """
            }
        }

        stage('Check Test Results for Response Time') {
            steps {
                script {
                    def results = readFile(file: "${RESULTS_FILE}").split('\\n')
                    def header = results[0].split(',')
                    def responseTimeIndex = header.findIndexOf { it.trim() == 'elapsed' }
                    def failureFound = false

                    if (responseTimeIndex == -1) {
                        error("The 'elapsed' column is not found in the results file.")
                    }

                    for (int i = 1; i < results.size(); i++) {
                        def line = results[i]
                        if (line.trim()) {
                            def columns = line.split(',')
                            def elapsedTime = columns[responseTimeIndex]?.toInteger()

                            if (elapsedTime > MAX_RESPONSE_TIME_MS) {
                                echo "Request exceeded maximum response time: ${elapsedTime} ms (Threshold: ${MAX_RESPONSE_TIME_MS} ms)"
                                failureFound = true
                            }
                        }
                    }

                    if (failureFound) {
                        error("Max time reached: Response times exceeded the threshold of ${MAX_RESPONSE_TIME_MS} ms.")
                    } else {
                        echo 'All requests completed within the acceptable response time.'
                    }
                }
            }
        }

        stage('Publish Report') {
            steps {
                perfReport filterRegex: '', sourceDataFiles: "${RESULTS_FILE}", graphType: 'jtl'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Max time reached for one or more requests.'
        }
    }
}
