pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/naveengadde123/JMeterTesing.git'  // Your GitHub repository URL
        BRANCH = 'main'  // Your branch name
        CREDENTIALS_ID = '1d54e951-e865-4582-a541-e726548cfefd'  // Your credentials ID
        JMETER_HOME = 'C:/JMeter/apache-jmeter-5.6.3/apache-jmeter-5.6.3'  // Adjust this path to your JMeter installation
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning the Git repository...'
                // Cloning the specified branch from the repository and using the credentials
                git branch: "${BRANCH}", url: "${GIT_REPO}", credentialsId: "${CREDENTIALS_ID}"
            }
        }

        stage('Test JMeter') {
            steps {
                script {
                    try {
                        echo 'Running JMeter tests...'
                        // Running JMeter tests with the .jmx file after cloning
                        bat """
                            "${JMETER_HOME}/bin/jmeter.bat" -n -t "${JMETER_HOME}/bin/Test.jmx" -l "C:\\Training\\results.jtl" -JDuration=1 -Jusers=3 -JCSVFilePath="C:\\Training\\input.csv"
                        """
                    } catch (Exception e) {
                        echo "An error occurred during the test execution. Terminating after 2 seconds..."
                        // Timeout of 2 seconds to simulate stopping after the error
                        timeout(time: 2, unit: 'SECONDS') {
                            // Perform the termination after 2 seconds
                            echo 'Terminating the process after 2 seconds due to error.'
                            // Optionally, you can add the logic to kill the process or handle it
                            bat "taskkill /F /IM jmeter.bat" // Kill the JMeter process if it's still running
                        }
                        // Re-throw the error to ensure pipeline fails
                        throw e
                    }
                }
            }
        }

        stage('Publish Report') {
            steps {
                echo 'Publishing performance report...'
                // Publish the performance report based on the .jtl file
                perfReport filterRegex: '', sourceDataFiles: 'C:\\Training\\results.jtl'
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
