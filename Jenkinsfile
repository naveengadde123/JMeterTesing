pipeline {
    agent any

    environment {
        JMETER_HOME = 'C:/JMeter/apache-jmeter-5.6.3/apache-jmeter-5.6.3'
        JMX_FILE = 'C:/JMeter/apache-jmeter-5.6.3/apache-jmeter-5.6.3/bin/Test.jmx'
        RESULTS_FILE = 'C:/Training/results.jtl'
    }

    stages {
        stage('Run JMeter Tests') {
            steps {
                script {
                    // Run the JMeter test
                    bat """
                        "${JMETER_HOME}/bin/jmeter.bat" -n -t "${JMX_FILE}" -l "${RESULTS_FILE}" -f
                    """
                }
            }
        }

        stage('Find Slowest API Request') {
            steps {
                script {
                    // Read the results file
                    def results = readFile(file: "${RESULTS_FILE}").split('\n')
                    def header = results[0].split(',')
                    def elapsedIndex = header.findIndexOf { it.trim() == 'elapsed' }
                    def urlIndex = header.findIndexOf { it.trim() == 'URL' }

                    if (elapsedIndex == -1 || urlIndex == -1) {
                        error("The 'elapsed' or 'URL' columns are not found in the results file.")
                    }

                    def maxElapsedTime = 0
                    def slowestApi = ''
                    
                    // Loop through the results and find the slowest API request
                    for (int i = 1; i < results.size(); i++) {
                        def line = results[i]
                        if (line.trim()) {
                            def columns = line.split(',')
                            def elapsedTime = columns[elapsedIndex]?.trim()
                            def apiUrl = columns[urlIndex]?.trim()

                            // Convert elapsedTime to integer
                            try {
                                elapsedTime = Integer.parseInt(elapsedTime)
                            } catch (Exception e) {
                                echo "Invalid elapsed time at line ${i}: ${columns[elapsedIndex]}"
                                continue
                            }

                            // Find the maximum elapsed time and corresponding URL
                            if (elapsedTime > maxElapsedTime) {
                                maxElapsedTime = elapsedTime
                                slowestApi = apiUrl
                            }
                        }
                    }

                    // Output the slowest API and its response time
                    echo "The slowest API request is: ${slowestApi} with a response time of ${maxElapsedTime} ms"
                }
            }
        }
    }

    post {
        success {
            echo 'Test completed successfully!'
        }
        failure {
            echo 'Test failed.'
        }
    }
}
