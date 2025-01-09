pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/naveengadde123/JMeterTesing.git'
        BRANCH = 'main'
        CREDENTIALS_ID = '1d54e951-e865-4582-a541-e726548cfefd'
        JMETER_HOME = 'C:/JMeter/apache-jmeter-5.6.3/apache-jmeter-5.6.3'
        JMX_FILE = 'C:/JMeter/apache-jmeter-5.6.3/apache-jmeter-5.6.3/bin/Test.jmx'
        RESULTS_FILE = 'C:/Training/results.jtl'
        LOCAL_IP = '127.0.0.1'
        LOCAL_PORT = 5555
        MAX_EXECUTION_TIME = 10000 // Set the maximum allowed time in milliseconds (5 seconds)
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: "${BRANCH}", url: "${GIT_REPO}", credentialsId: "${CREDENTIALS_ID}"
                echo 'Cloning the repository...'
            }
        }

        stage('Check Connection to Localhost') {
            steps {
                script {
                    def connectionResult = bat(script: """
                        powershell -Command "Test-NetConnection -ComputerName localhost -Port ${LOCAL_PORT}"
                    """, returnStdout: true).trim()

                    if (connectionResult.contains('TcpTestSucceeded : True')) {
                        echo "Connection to localhost:5555 is successful!"
                    } else {
                        error "Failed to connect to localhost:5555"
                    }
                }
            }
        }

        stage('Run JMeter Tests') {
            steps {
                script {
                    // Record the start time
                    def startTime = System.currentTimeMillis()

                    // Run the JMeter test
                    bat """
                        "${JMETER_HOME}/bin/jmeter.bat" -n -t "${JMX_FILE}" -l "${RESULTS_FILE}" -Jusers=3 -Jserver.host=${LOCAL_IP} -f
                    """

                    // Record the end time
                    def endTime = System.currentTimeMillis()

                    // Calculate the total time taken
                    def duration = endTime - startTime

                    // Print the total time to the console
                    echo "Total API test execution time: ${duration} ms"

                    // Ensure duration is compared as Long
                    if (duration > Long.parseLong(MAX_EXECUTION_TIME.toString())) {
                        error "Test execution time exceeded the maximum allowed time of ${MAX_EXECUTION_TIME} ms. Terminating the pipeline."
                    }
                }
            }
        }

        stage('Check Test Results') {
            steps {
                script {
                    def results = readFile(file: "${RESULTS_FILE}").split('\\n')
                    def failureFound = false
                    for (line in results) {
                        if (line.contains('false') || line.contains('500') || line.contains('404')) {
                            def apiDetails = line.split(',')
                            def requestName = apiDetails[0]
                            def status = apiDetails[1]
                            def errorMessage = apiDetails[3]
                            def responseMessage = apiDetails[4]
                            def failureMessage = apiDetails[5]
                            def errorDetails = "Request: ${requestName}, Status: ${status}, Error: ${errorMessage}, Response Message: ${responseMessage}, Failure Message: ${failureMessage}"
                            echo errorDetails
                            failureFound = true
                        }
                    }
                    if (failureFound) {
                        error("Pipeline terminated due to API failure. See the logs for details.")
                    } else {
                        echo 'All APIs passed successfully.'
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
            echo 'Pipeline failed. Please check the logs.'
        }
    }
}
