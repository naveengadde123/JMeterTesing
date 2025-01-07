pipeline {
    agent any
 
    environment {
        GIT_REPO = 'https://github.com/naveengadde123/JMeterTesing.git'  
        BRANCH = 'main'  
        CREDENTIALS_ID = '1d54e951-e865-4582-a541-e726548cfefd'  
        JMETER_HOME = 'C:/JMeter/apache-jmeter-5.6.3/apache-jmeter-5.6.3'  
        JMX_FILE = 'C:/JMeter/apache-jmeter-5.6.3/apache-jmeter-5.6.3/bin/Test.jmx'  
        RESULTS_FILE = 'C:/Training/results.jtl'  
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
 
        stage('Verify Test Results') {
            steps {
                echo 'Verifying test results for failures...'
                script {
                    // Check the results file for any errors
                    def failures = bat(
                        script: "findstr /c:\"s=\" /c:\"rc=\" ${RESULTS_FILE} | findstr /i /c:\" s=500 \" /c:\" s=404 \" | find /c /v \"\"",
                        returnStdout: true
                    ).trim()
                    if (failures.toInteger() > 0) {
                        error("JMeter tests failed with ${failures} HTTP request errors. Stopping pipeline.")
                    } else {
                        echo 'All HTTP requests passed successfully!'
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
        }
    }
}
