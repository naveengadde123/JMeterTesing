pipeline {
    agent any

    environment {
        JMETER_HOME = 'C:/JMeter/apache-jmeter-5.6.3/apache-jmeter-5.6.3'
        JMX_FILE = 'C:/JMeter/apache-jmeter-5.6.3/apache-jmeter-5.6.3/bin/Test.jmx'
        RESULTS_FILE = 'C:/Training/results.jtl'
        MAX_EXECUTION_TIME = 3000 // Set the maximum allowed time in milliseconds (10 seconds)
    }

    stages {
        stage('Run JMeter Tests') {
            steps {
                script {
                    // Record the start time
                    def startTime = System.currentTimeMillis()

                    // Run the JMeter test
                    bat """
                        "${JMETER_HOME}/bin/jmeter.bat" -n -t "${JMX_FILE}" -l "${RESULTS_FILE}" -f
                    """

                    // Record the end time
                    def endTime = System.currentTimeMillis()

                    // Calculate the total time taken
                    def duration = endTime - startTime

                    // Print the total time to the console
                    echo "Total API test execution time: ${duration} ms"

                    // Ensure duration is compared as Long
                    if (duration < Long.parseLong(MAX_EXECUTION_TIME.toString())) {
                        error "Test execution time exceeded the maximum allowed time of ${MAX_EXECUTION_TIME} ms. Terminating the pipeline."
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Test completed successfully within the allowed time!'
        }
        failure {
            echo 'Test failed or execution time exceeded the maximum limit.'
        }
    }
}
