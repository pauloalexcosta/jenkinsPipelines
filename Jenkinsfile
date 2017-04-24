pipeline {
  agent any
  stages {
    stage('A Fresh Workspace') {
      steps {
        script {
          fileOperations([folderDeleteOperation('Reports'), fileDeleteOperation(excludes: '', includes: 'Report.zip'), fileDeleteOperation(excludes: '', includes: 'TaskManager.csv')])
        }
        
      }
    }
    stage('Run JMeter Test') {
      steps {
        bat(script: 'E:/JMeter/apache-jmeter-3.1/bin/jmeter.bat -n -t E:/JMeter/Resources/Webinar/Scripts/TaskManager.jmx -l TaskManager.csv', encoding: 'UTF8')
        archiveArtifacts(artifacts: 'TaskManagerResults.csv', onlyIfSuccessful: true)
      }
    }
    stage('Create Reporting') {
      steps {
        parallel(
          "Run Performance Plugin": {
            script {
              performanceReport compareBuildPrevious: true, configType: 'ART', errorFailedThreshold: 3, errorUnstableResponseTimeThreshold: '', errorUnstableThreshold: 1, failBuildIfNoResultFile: true, modeOfThreshold: true, modePerformancePerTestCase: true, modeThroughput: true, nthBuildNumber: 0, parsers: [[$class: 'JMeterCsvParser', delimiter: ',', glob: 'TaskManager.csv', pattern: 'timeStamp,elapsed,url,responseCode,responseMessage,threadName,dataType,success,failureMessage,bytes,sentBytes,grpThreads,allThreads,Latency,IdleTime,Connect', skipFirstLine: true]], relativeFailedThresholdNegative: 100, relativeFailedThresholdPositive: 100, relativeUnstableThresholdNegative: 50, relativeUnstableThresholdPositive: 50
            }
            
            
          },
          "Create Jmeter HTML": {
            bat(script: 'E:/JMeter/apache-jmeter-3.1/bin/jmeter.bat -g E:/JMeter/Resources/Webinar/Results/TaskManagerResults.csv -o Reports/', encoding: 'utf8', returnStdout: true)
            bat(script: 'del /f "E:/JMeter/Resources/Webinar/Results/TaskManagerResults.csv"')
            
          }
        )
      }
    }
    stage('Archive the HTML Report') {
      steps {
        zip(zipFile: 'Report.zip', archive: true, dir: 'Reports')
      }
    }
  }
}
