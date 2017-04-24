pipeline {
  agent any
  stages {
    stage('A Fresh Workspace') {
      steps {
        script {
          fileOperations([folderDeleteOperation('Reports'), fileDeleteOperation(excludes: '', includes: 'Report.zip')])
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
              performanceReport compareBuildPrevious: true, configType: 'ART', errorFailedThreshold: '3', errorUnstableResponseTimeThreshold: '5', errorUnstableThreshold: '1', failBuildIfNoResultFile: false, modeOfThreshold: true, modePerformancePerTestCase: false, modeThroughput: true, nthBuildNumber: 0, parsers: [[$class: 'JMeterCsvParser', delimiter: ',', glob: 'TaskManager.csv', pattern: 'timeStamp,elapsed,url,responseCode,responseMessage,threadName,dataType,success,failureMessage,bytes,sentBytes,grpThreads,allThreads,Latency,IdleTime,Connect', skipFirstLine: true]], relativeFailedThresholdNegative: '2000', relativeFailedThresholdPositive: '2000', relativeUnstableThresholdNegative: '1000', relativeUnstableThresholdPositive: '1000'
            }
            
            
          },
          "Create Jmeter HTML": {
            bat(script: 'E:/JMeter/apache-jmeter-3.1/bin/jmeter.bat -g E:/JMeter/Resources/Webinar/Results/TaskManagerResults.csv -o Reports/', encoding: 'utf8', returnStdout: true)
            bat 'del /f E:\\JMeter\\Resources\\Webinar\\Results\\TaskManagerResults.csv'            
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
