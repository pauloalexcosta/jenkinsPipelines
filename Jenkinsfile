pipeline {
  agent any
  stages {
    stage('A Fresh Workspace') {
      steps {
        script {
          fileOperations([folderDeleteOperation('Reports'), fileDeleteOperation(excludes: '', includes: 'Report.zip')
        }
        
      }
    }
    stage('Run JMeter Test') {
      steps {
        bat(script: 'E:/JMeter/apache-jmeter-3.1/bin/jmeter.bat -n -t E:/JMeter/Resources/CTT/PhaseI/AppCTT.jmx -l test.jtl -e -o Reports', encoding: 'UTF8')
        archiveArtifacts(artifacts: 'test.jtl', allowEmptyArchive: true, onlyIfSuccessful: true)
      }
    }
    stage('Create Reporting') {
      steps {
        parallel(
          "Run Performance Plugin": {
            script {
              performanceReport compareBuildPrevious: true, configType: 'ART', errorFailedThreshold: 3, errorUnstableResponseTimeThreshold: '', errorUnstableThreshold: 1, failBuildIfNoResultFile: true, modeOfThreshold: true, modePerformancePerTestCase: true, modeThroughput: true, nthBuildNumber: 0, parsers: [[$class: 'JMeterCsvParser', delimiter: ',', glob: 'test.jtl', pattern: 'timeStamp,elapsed,url,responseCode,responseMessage,threadName,dataType,success,failureMessage,bytes,sentBytes,grpThreads,allThreads,Latency,IdleTime,Connect', skipFirstLine: true]], relativeFailedThresholdNegative: 100, relativeFailedThresholdPositive: 100, relativeUnstableThresholdNegative: 20, relativeUnstableThresholdPositive: 20
            }
            
            
          },
          "Create Jmeter HTML": {
            bat(script: 'E:/JMeter/apache-jmeter-3.1/bin/jmeter.bat -g test.jtl -o Reports/', encoding: 'utf8', returnStdout: true)
            
          }
        )
      }
    }
    stage('Archive the HTML Report') {
      steps {
        zip(zipFile: 'Report.zip', archive: true, dir: 'Reports')
      }
    }
    stage('Email') {
      steps {
        emailext(subject: 'Jmeter Test has finished', body: 'Take it.', to: 'paulo.alexandre@gmail.com', attachmentsPattern: 'Report.zip')
      }
    }
  }
}