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
                // Run JMeter tests on Windows
                bat '''
                    "C:\\JMeter\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3\\bin\\jmeter.bat" -n -t "C:\\JMeter\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3\\bin\\Test.jmx" -l "C:\\Training\\results.jtl" -JDuration=1 -Jusers=3 -JCSVFilePath="C:\\Training\\input.csv"
                '''
            }
        }

        stage('Publish Report') {
            steps {
                echo 'Publishing performance report...'
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
