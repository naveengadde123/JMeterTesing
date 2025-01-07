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
                script {
                    def result = bat returnStatus: true, script: """
                        "${JMETER_HOME}/bin/jmeter.bat" -n -t "${JMX_FILE}" -l "${RESULTS_FILE}" -JDuration=1 -Jusers=3
                    """
                    if (result != 0) {
                        echo 'Error detected in .jmx file! Limiting test duration to 2 seconds...'
                        bat """
                            "${JMETER_HOME}/bin/jmeter.bat" -n -t "${JMX_FILE}" -l "${RESULTS_FILE}" -JDuration=2
                        """
                        error('JMeter test terminated due to error in .jmx file.')
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
