pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning the repository...'
                git branch: 'main', credentialsId: '1d54e951-e865-4582-a541-e726548cfefd', url: 'https://github.com/naveengadde123/JMeterTesing.git'
            }
        }

        stage('Test') {
            steps {
                echo 'Running JMeter tests...'
                script {
                    try {
                        // Run JMeter tests on Windows
                        bat '''
                            "C:\\JMeter\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3\\bin\\jmeter.bat" -n -t "C:\\JMeter\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3\\bin\\Test.jmx" -l "C:\\Training\\results.jtl"
                        '''
                        
                        // Parse the JMeter results file (.jtl) to check for failed requests
                        def resultsFile = 'C:\\Training\\results.jtl'
                        def failedRequests = readFile(resultsFile).split("\n").findAll { line -> 
                            // Look for failed requests (where response code != 200 or status = failed)
                            line.contains('false') || line.contains('500') || line.contains('404') 
                        }

                        if (failedRequests) {
                            echo "Failed API Requests:"
                            failedRequests.each { request ->
                                def columns = request.split(",")
                                def requestName = columns[0]  // Assuming request name or URL is the first column
                                def status = columns[1]  // Assuming status is in the second column
                                def errorMessage = columns[2]  // Assuming error message is in the third column

                                echo "Request: ${requestName}, Status: ${status}, Error: ${errorMessage}"
                            }
                            error "JMeter test execution failed due to API errors."
                        } else {
                            echo "All APIs passed successfully!"
                        }
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        echo "JMeter test execution failed: ${e.getMessage()}"
                        throw e  // Terminate the pipeline
                    }
                }
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
