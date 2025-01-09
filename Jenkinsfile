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
        LOCAL_IP = '127.0.0.1'
        LOCAL_PORT = 5555
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

                    bat '''
                        "C:\\JMeter\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3\\bin\\jmeter.bat" -n -t "C:\\JMeter\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3\\bin\\Test.jmx" -l "C:\\Training\\results.jtl"
                    '''
                    
                    def resultsFile = 'C:\\Training\\results.jtl'
                    def failedRequests = readFile(resultsFile).split("\n").findAll { line -> 
                        line.contains('false') || line.contains('500') || line.contains('404') 
                    }

                    if (connectionResult.contains('TcpTestSucceeded : True')) {
                        echo "Connection to localhost:5555 is successful!"
                        if (failedRequests) {
                            echo "Failed API Requests (detailed info):"
                            failedRequests.each { request ->
                                def columns = request.split(",")
                                def requestName = columns[0]  // Assuming request name or URL is the first column
                                def status = columns[1]  // Assuming status is in the second column
                                def errorMessage = columns[3]  // Assuming error message is in the third column
                                def responseMessage = columns[4]  // Assuming response message is in the fourth column
                                def failureMessage = columns[5]  // Assuming failure message is in the fifth column

                                echo "Request: ${requestName}, Status: ${status}, Error: ${errorMessage}, Response Message: ${responseMessage}, Failure Message: ${failureMessage}"
                            }
                            error "JMeter test execution failed due to API errors."
                        } else {
                            echo "All APIs passed successfully!"
                        }
                    } else {
                        error "Failed to connect to localhost:5555"
                    }
                }
            }
        }

        stage('Run JMeter Tests') {
            steps {
                bat """
                    "${JMETER_HOME}/bin/jmeter.bat" -n -t "${JMX_FILE}" -l "${RESULTS_FILE}" -JDuration=1 -Jusers=3 -Jserver.host=${LOCAL_IP}
                """
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
            script {
                if (fileExists("${ERROR_LOG}")) {
                    echo "Error details:\\n" + readFile(file: "${ERROR_LOG}")
                }
            }
        }
    }
}
