pipeline {
  agent any
  stages {
    stage('Run JMeter Test') {
      steps {
        bat(script: 'E:/JMeter/apache-jmeter-3.1/bin/jmeter.bat -n -t E:/JMeter/Resources/CTT/PhaseI/AppCTT.jmx -l test.jtl', returnStdout: true)
        archiveArtifacts(artifacts: 'test.jtl', allowEmptyArchive: true, onlyIfSuccessful: true)
      }
    }
    stage('Create Reporting') {
      steps {
        parallel(
          "Run Performance Plugin": {
            script {
              performanceReport compareBuildPrevious: true, configType: 'ART', errorFailedThreshold: 10, errorUnstableResponseTimeThreshold: '', errorUnstableThreshold: 5, failBuildIfNoResultFile: false, ignoreFailedBuilds: true, ignoreUnstableBuilds: true, modeOfThreshold: false, modePerformancePerTestCase: true, modeThroughput: false, nthBuildNumber: 0, parsers: [[$class: 'JMeterCsvParser', delimiter: ',', glob: 'test.jtl', pattern: 'timeStamp,elapsed,url,responseCode,responseMessage,threadName,dataType,success,failureMessage,bytes,sentBytes,grpThreads,allThreads,Latency,IdleTime,Connect', skipFirstLine: true]], relativeFailedThresholdNegative: 6, relativeFailedThresholdPositive: 10, relativeUnstableThresholdNegative: 1, relativeUnstableThresholdPositive: 5
            }
            
            
          },
          "Create Jmeter HTML": {
            bat 'E:/JMeter/apache-jmeter-3.1/bin/jmeter.bat -g test.jtl -o Reports/'
            
          }
        )
      }
    }
    stage('Email Me') {
      steps {
        emailext(subject: 'Tests Completed', attachLog: true, attachmentsPattern: 'test.jtl', body: 'It\'s done!', to: 'paulo.costa@outmail.com')
      }
    }
  }
}